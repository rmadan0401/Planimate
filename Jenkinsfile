pipeline {
    agent any
    environment {
        NVM_DIR = "/home/ext_rmadan_vecv_in/.nvm"
        NODE_VERSION = 'v18.20.0'
        PATH = "${WORKSPACE}/.nvm/versions/node/${NODE_VERSION}/bin:${PATH}"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/rmadan0401/feelio.git', credentialsId: 'github-credentials'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh '''
                    export NVM_DIR="${WORKSPACE}/.nvm"
                    . $NVM_DIR/nvm.sh
                    nvm use $NODE_VERSION
                    node -v
                    npm -v
                    npm install
                '''
            }
        }
        stage('Build') {
            steps {
                sh '''
                    export NVM_DIR="${WORKSPACE}/.nvm"
                    . $NVM_DIR/nvm.sh
                    nvm use $NODE_VERSION
                    npm run build
                '''
            }
        }
    }
}
