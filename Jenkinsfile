pipeline {
    agent any

    environment {
        NVM_DIR = '/home/ext_rmadan_vecv_in/.nvm'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/rmadan0401/Planimate.git', credentialsId: 'github-credentials'
            }
        }

        stage('Set Node Version') {
            steps {
                sh '''
                    export NVM_DIR="$NVM_DIR"
                    [ -s "$NVM_DIR/nvm.sh" ] && \\. "$NVM_DIR/nvm.sh"
                    nvm use 18.20.7
                    node -v
                    npm -v
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    export NVM_DIR="$NVM_DIR"
                    [ -s "$NVM_DIR/nvm.sh" ] && \\. "$NVM_DIR/nvm.sh"
                    nvm use 18.20.7
                    corepack enable
                    yarn --version
                    yarn install
                '''
            }
        }

        stage('Build Android') {
            steps {
                sh '''
                    export NVM_DIR="$NVM_DIR"
                    [ -s "$NVM_DIR/nvm.sh" ] && \\. "$NVM_DIR/nvm.sh"
                    nvm use 18.20.7
                    cd android
                    ./gradlew assembleDebug
                '''
            }
        }
    }
}
