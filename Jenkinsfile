pipeline {
    agent any

    environment {
        // NVM & Node.js Setup
        NVM_DIR = "/home/ext_rmadan_vecv_in/.nvm"
        PATH = "${NVM_DIR}/versions/node/v18.20.7/bin:/home/ext_rmadan_vecv_in/.yarn/bin:$PATH"

        // Android SDK Setup
        ANDROID_SDK_ROOT = "/home/ext_rmadan_vecv_in/android-sdk"
        PATH = "${ANDROID_SDK_ROOT}/cmdline-tools/tools/bin:${ANDROID_SDK_ROOT}/platform-tools:${ANDROID_SDK_ROOT}/emulator:$PATH"

        // GitHub Credentials
        GIT_CREDENTIALS_ID = "github-credentials"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/wfarat/Planimate.git',
                    credentialsId: "${env.GIT_CREDENTIALS_ID}"
            }
        }

        stage('Setup Node & Yarn') {
            steps {
                echo "Loading NVM and setting Node.js v18"

                // Load NVM and use Node
                sh '''
                    export NVM_DIR=/home/ext_rmadan_vecv_in/.nvm
                    . $NVM_DIR/nvm.sh
                    nvm use 18
                    node -v
                    npm -v
                '''

                // Check Yarn
                sh '''
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
                    
                    export ANDROID_SDK_ROOT=/home/ext_rmadan_vecv_in/android-sdk
                    export PATH=$ANDROID_SDK_ROOT/platform-tools:$ANDROID_SDK_ROOT/emulator:$PATH

                    yarn android
                '''
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
