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
                echo 'Checking and fixing folder structure...'
                sh '''
                    cd android/app/src/main/java/com

                    # Remove duplicate plannerapp folder if exists
                    if [ -d "planimate/plannerapp" ]; then
                        rm -rf planimate/plannerapp
                    fi

                    # Rename incorrect folder
                    if [ -d "plannerapp" ] && [ ! -d "planimate" ]; then
                        mv plannerapp planimate
                    fi

                    echo "Folder structure after fix:"
                    find . -type d
                '''

                echo 'Cleaning Gradle cache...'
                sh '''
                    cd android
                    chmod +x gradlew
                    ./gradlew clean
                    ./gradlew --stop
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

        stage('Firebase Auth Fix') {
            steps {
                echo 'Creating empty debug folder for Firebase Auth...'
                sh '''
                    mkdir -p node_modules/@react-native-firebase/auth/android/src/debug/java
                    echo "Created empty debug folder for Firebase Auth."
                '''
            }
        }

        stage('Build Android APK') {
            steps {
                sh '''
                    # Start Metro bundler
                    yarn start --reset-cache &
                    sleep 10

                    cd android
                    chmod +x gradlew

                    # Disable incremental Kotlin & Build
                    ./gradlew assembleDebug \
                        -Porg.gradle.jvmargs="-Xmx2048m" \
                        -Pkotlin.incremental=false \
                        --no-daemon --stacktrace --info --debug

                    # Stop Metro bundler
                    kill $(lsof -t -i:8081 || true)

                    # List APKs
                    echo "APK generated:"
                    ls -l app/build/outputs/apk/debug/
                '''
            }
        }

        stage('Archive APK') {
            steps {
                archiveArtifacts artifacts: 'android/app/build/outputs/apk/debug/*.apk', fingerprint: true
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished!'
        }
        failure {
            echo 'Pipeline failed! Check logs!'
        }
    }
}
