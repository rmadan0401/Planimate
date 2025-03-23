pipeline {
    agent any

    environment {
        // NVM Setup
        NVM_DIR = "/home/ext_rmadan_vecv_in/.nvm"

        // Android SDK Setup
        ANDROID_SDK_ROOT = "/home/ext_rmadan_vecv_in/android-sdk"

        // GitHub Credentials
        GIT_CREDENTIALS_ID = "github-credentials"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/rmadan0401/Planimate.git',
                    credentialsId: "${env.GIT_CREDENTIALS_ID}"
            }
        }

        stage('Setup Node & Yarn') {
            steps {
                echo "Loading NVM and setting Node.js v18"
                sh '''
                    export NVM_DIR=/home/ext_rmadan_vecv_in/.nvm
                    . $NVM_DIR/nvm.sh
                    nvm use 18
                    export PATH=$NVM_DIR/versions/node/v18.20.7/bin:$PATH
                    
                    node -v
                    npm -v
                    
                    yarn --version || npm install -g yarn
                    yarn set version 3.6.4
                    yarn --version
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    export NVM_DIR=/home/ext_rmadan_vecv_in/.nvm
                    . $NVM_DIR/nvm.sh
                    nvm use 18
                    export PATH=$NVM_DIR/versions/node/v18.20.7/bin:$PATH

                    yarn install
                '''
            }
        }

        stage('Start Metro Bundler') {
            steps {
                sh '''
                    export NVM_DIR=/home/ext_rmadan_vecv_in/.nvm
                    . $NVM_DIR/nvm.sh
                    nvm use 18
                    export PATH=$NVM_DIR/versions/node/v18.20.7/bin:$PATH

                    nohup yarn start > metro.log 2>&1 &
                '''
            }
        }

        stage('Run Android Build') {
            steps {
                sh '''
                    export NVM_DIR=/home/ext_rmadan_vecv_in/.nvm
                    . $NVM_DIR/nvm.sh
                    nvm use 18
                    export PATH=$NVM_DIR/versions/node/v18.20.7/bin:$PATH

                    export ANDROID_SDK_ROOT=/home/ext_rmadan_vecv_in/android-sdk
                    export PATH=$ANDROID_SDK_ROOT/platform-tools:$ANDROID_SDK_ROOT/emulator:$PATH:$PATH

                    yarn android
                '''
            }
        }

        stage('Archive APK') {
            steps {
                script {
                    // Archive all APK files under android/app/build/outputs/
                    archiveArtifacts artifacts: 'android/app/build/outputs/**/*.apk', fingerprint: true
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up background Metro bundler...'
            sh "pkill -f 'yarn start' || true"
        }
    }
}
