pipeline {
    agent any
    environment {
        NODE_VERSION = "v18.20.7"
        NODE_DIR = "${WORKSPACE}/.node"
        PATH = "${WORKSPACE}/.node/bin:${env.PATH}"
    }
    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'github-credentials', url: 'https://github.com/rmadan0401/Planimate.git', branch: 'main'
            }
        }
        stage('Setup NodeJS') {
            steps {
                sh '''
                    echo "Downloading NodeJS..."
                    curl -o node.tar.xz https://nodejs.org/dist/${NODE_VERSION}/node-${NODE_VERSION}-linux-x64.tar.xz
                    mkdir -p ${NODE_DIR}
                    tar -xf node.tar.xz --strip-components=1 -C ${NODE_DIR}
                    rm node.tar.xz
                    node -v
                    npm -v
                '''
            }
        }
        stage('Install Dependencies') {
            steps {
                sh '''
                    npm install
                '''
            }
        }
        stage('Build') {
            steps {
                sh '''
                    npm run build
                '''
            }
        }
    }
}
