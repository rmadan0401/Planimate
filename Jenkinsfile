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

                    if [ -d "planimate/plannerapp" ]; then
                        echo "Found duplicate plannerapp inside planimate. Removing..."
                        rm -rf planimate/plannerapp
                    fi

                    if [ -d "plannerapp" ] && [ ! -d "planimate" ]; then
                        echo "Renaming plannerapp folder to planimate..."
                        mv plannerapp planimate
                    fi

                    echo "Final folder structure:"
                    find . -type d
                '''

                echo 'Cleaning Gradle cache to avoid stale references...'
                sh '''
                    cd android
                    chmod +x gradlew
                    ./gradlew clean || true
                    ./gradlew --stop || true
                '''
            }
        }

        stage('Install Node & Yarn Locally') {
            steps {
                sh '''
                    mkdir -p ${NODE_DIR}
                    curl -o node.tar.xz https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}-linux-x64.tar.xz
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

        stage('Accept Android SDK Licenses') {
            steps {
                sh '''
                    echo "Accepting Android SDK Licenses..."
                    yes | ${ANDROID_HOME}/cmdline-tools/latest/bin/sdkmanager --licenses || true
                '''
            }
        }

        stage('Yarn Install') {
            steps {
                sh 'yarn install'
            }
        }

        stage('Fix gradlew Permissions') {
            steps {
                sh 'chmod +x android/gradlew'
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

        stage('Build Android APK') {
            steps {
                sh '''
                    cd android

                    # Ensure gradlew permission
                    chmod +x gradlew

                    # Clean previous builds
                    ./gradlew clean || true

                    echo "Building APK..."
                    ./gradlew assembleDebug --no-daemon --stacktrace --info --warning-mode all
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
