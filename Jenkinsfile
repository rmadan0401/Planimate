pipeline {
    agent any
    environment {
        NVM_DIR = "/home/ext_rmadan_vecv_in/.nvm"
    }
    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'github-credentials', url: 'https://github.com/rmadan0401/feelio.git', branch: 'main'
            }
        }
        stage('Setup Node') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    source "$NVM_DIR/nvm.sh"
                    nvm use 18.20.7
                    export PATH="$NVM_DIR/versions/node/v18.20.7/bin:$PATH"
                    node -v
                    npm -v
                '''
            }
        }
        stage('Install Dependencies') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    source "$NVM_DIR/nvm.sh"
                    nvm use 18.20.7
                    export PATH="$NVM_DIR/versions/node/v18.20.7/bin:$PATH"
                    npm install
                '''
            }
        }
        stage('Build') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    source "$NVM_DIR/nvm.sh"
                    nvm use 18.20.7
                    export PATH="$NVM_DIR/versions/node/v18.20.7/bin:$PATH"
                    npm run build
                '''
            }
        }
    }
}
