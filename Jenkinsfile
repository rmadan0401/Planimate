pipeline {
    agent any

    environment {
        NVM_DIR = "$HOME/.nvm"
        NODE_VERSION = "18.19.1" // specify the installed Node version
        YARN_VERSION = "3.6.4"
        ANDROID_SDK_ROOT = "/opt/android-sdk" // Update this path as per your Jenkins setup
        JAVA_HOME = "/usr/lib/jvm/java-11-openjdk-amd64" // Update if needed
        PATH = "${env.NVM_DIR}/versions/node/v${NODE_VERSION}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'github-credentials', url: 'https://github.com/rmadan0401/Planimate.git'
            }
        }

        stage('Set Node & Yarn') {
            steps {
                script {
                    sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
                    nvm use $NODE_VERSION

                    corepack enable
                    yarn set version ${YARN_VERSION}
                    '''
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                export NVM_DIR="$HOME/.nvm"
                [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
                nvm use $NODE_VERSION
                
                yarn install
                '''
            }
        }

        stage('Run Lint & Tests') {
            steps {
                sh '''
                export NVM_DIR="$HOME/.nvm"
                [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
                nvm use $NODE_VERSION

                yarn lint
                yarn test || echo "Tests failed, but continuing build"
                '''
            }
        }

        stage('Build Android') {
            steps {
                sh '''
                export NVM_DIR="$HOME/.nvm"
                [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
                nvm use $NODE_VERSION
                
                cd android
                ./gradlew clean assembleDebug
                '''
            }
        }

        stage('Archive APK') {
            steps {
                archiveArtifacts artifacts: '**/app/build/outputs/apk/debug/app-debug.apk', fingerprint: true
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
