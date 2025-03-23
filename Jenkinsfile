pipeline {
    agent any

    environment {
        ANDROID_HOME = "/home/ext_rmadan_vecv_in/android-sdk"
        PATH = "${PATH}:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/emulator"
        NODE_OPTIONS = "--max_old_space_size=4096"
        NVM_DIR = "${HOME}/.nvm"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/rmadan0401/Planimate.git', credentialsId: 'github-credentials'
            }
        }

        stage('Setup Node & Yarn') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                    nvm install 18
                    nvm use 18
                    node -v
                    npm install -g yarn@3.6.4
                    yarn -v
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                    nvm use 18
                    yarn install
                '''
            }
        }

        stage('Build Android App') {
            steps {
                dir('android') {
                    sh '''
                        export NVM_DIR="$HOME/.nvm"
                        [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                        nvm use 18
                        chmod +x gradlew
                        ./gradlew assembleDebug
                    '''
                }
            }
        }

        stage('Archive APK') {
            steps {
                archiveArtifacts artifacts: 'android/app/build/outputs/apk/debug/app-debug.apk', fingerprint: true
            }
        }
    }
}
