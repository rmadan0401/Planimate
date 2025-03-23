pipeline {
    agent any

    environment {
        NVM_DIR = "${WORKSPACE}/.nvm"
        NODE_VERSION = "18.20.0"
        YARN_VERSION = "3.6.4"
        GITHUB_CREDENTIALS = "github-credentials"
        ANDROID_HOME = "/var/lib/jenkins/Android/Sdk"
        PATH = "${WORKSPACE}/.nvm/versions/node/v${NODE_VERSION}/bin:$PATH"
    }

    tools {
        // No global tools since we are using NVM
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: "github-credentials", url: 'https://github.com/rmadan0401/Planimate.git', branch: 'main'
            }
        }

        stage('Setup Node & Yarn') {
            steps {
                sh '''
                    export NVM_DIR="$WORKSPACE/.nvm"
                    mkdir -p $NVM_DIR
                    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
                    . $NVM_DIR/nvm.sh
                    nvm install $NODE_VERSION
                    nvm use $NODE_VERSION
                    corepack enable
                    corepack prepare yarn@$YARN_VERSION --activate
                    yarn -v
                    node -v
                    npm -v
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    export NVM_DIR="$WORKSPACE/.nvm"
                    . $NVM_DIR/nvm.sh
                    nvm use $NODE_VERSION
                    yarn install
                '''
            }
        }

        stage('Build Android APK') {
            steps {
                sh '''
                    export NVM_DIR="$WORKSPACE/.nvm"
                    . $NVM_DIR/nvm.sh
                    nvm use $NODE_VERSION
                    cd android
                    ./gradlew assembleRelease
                '''
            }
        }

        stage('Archive APK') {
            steps {
                archiveArtifacts artifacts: 'android/app/build/outputs/apk/release/app-release.apk', allowEmptyArchive: false
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
