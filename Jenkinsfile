pipeline {
    agent any

    environment {
        NODE_VERSION = "18.20.7"
        YARN_VERSION = "3.6.4"
        NODE_DIR = "${WORKSPACE}/.node"
        ANDROID_HOME = "/home/ext_rmadan_vecv_in/android-sdk"
        PATH = "${NODE_DIR}/bin:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/cmdline-tools/latest/bin:${PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/rmadan0401/Planimate.git', credentialsId: 'github-credentials'
            }
        }

        stage('Fix Package Folder Structure') {
            steps {
                echo 'Checking and fixing folder structure if required...'
                sh '''
                    cd android/app/src/main/java/com

                    # If plannerapp folder exists inside planimate, remove it
                    if [ -d "planimate/plannerapp" ]; then
                        echo "Found duplicate plannerapp inside planimate. Removing..."
                        rm -rf planimate/plannerapp
                    fi

                    # If plannerapp exists incorrectly, rename
                    if [ -d "plannerapp" ] && [ ! -d "planimate" ]; then
                        echo "Renaming plannerapp folder to planimate..."
                        mv plannerapp planimate
                    fi

                    echo "Final folder structure:"
                    find . -type d
                '''
            }
        }

        stage('Install Node & Yarn Locally') {
            steps {
                sh '''
                    mkdir -p ${NODE_DIR}
                    curl -o node.tar.xz https://nodejs.org/dist/v18.20.7/node-v18.20.7-linux-x64.tar.xz
                    tar -xf node.tar.xz --strip-components=1 -C ${NODE_DIR}
                    rm node.tar.xz

                    ${NODE_DIR}/bin/node -v
                    ${NODE_DIR}/bin/npm -v

                    corepack enable
                    corepack prepare yarn@${YARN_VERSION} --activate
                    yarn -v
                '''
            }
        }

        stage('Yarn Install') {
            steps {
                sh 'yarn install'
            }
        }

        stage('Create local.properties') {
            steps {
                sh '''
                    echo "sdk.dir=${ANDROID_HOME}" > android/local.properties
                    cat android/local.properties
                '''
            }
        }

        stage('Prepare Firebase & Clean Environment') {
            steps {
                sh '''
                    # Fix Firebase missing debug folder issue
                    mkdir -p node_modules/@react-native-firebase/auth/android/src/debug/java
                    echo "Created empty debug folder for Firebase Auth."

                    # Ensure gradlew permissions
                    chmod +x android/gradlew

                    # Disable Kotlin incremental builds (avoiding workers issue)
                    export ORG_GRADLE_PROJECT_kotlin.incremental=false
                    export KOTLIN_DAEMON_JVMARGS="-Xmx2048m"

                    # Clean Gradle caches
                    cd android
                    ./gradlew cleanBuildCache || true
                    ./gradlew clean || true
                '''
            }
        }

        stage('Build Android APK') {
            steps {
                sh '''
                    # Start Metro bundler in background
                    yarn start --reset-cache &

                    # Wait for Metro to be ready
                    sleep 10

                    # Navigate to android directory
                    cd android

                    # Build APK with detailed logs and increased memory settings
                    ./gradlew assembleDebug --no-daemon --stacktrace --info --debug || true

                    # Stop Metro bundler (port 8081)
                    kill $(lsof -t -i:8081 || true)
                '''
            }
        }

        stage('Archive APK') {
            steps {
                archiveArtifacts artifacts: 'android/app/build/outputs/apk/debug/app-debug.apk', fingerprint: true
            }
        }
    }

    post {
        always {
            echo 'Build completed. Cleaning up if needed.'
        }
    }
}
