pipeline {
    agent any

    environment {
        ANDROID_SDK_ROOT = "/home/ext_rmadan_vecv_in/android-sdk"
        JAVA_HOME = "/usr/lib/jvm/java-11-openjdk-amd64"
        PATH = "${ANDROID_SDK_ROOT}/tools:${ANDROID_SDK_ROOT}/platform-tools:${JAVA_HOME}/bin:$PATH"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'github-credentials', url: 'https://github.com/rmadan0401/Planimate.git'
            }
        }

        stage('Install NodeJS & Yarn') {
            steps {
                sh '''
                    export NODE_VERSION=v18.20.2
                    export NVM_DIR=$WORKSPACE/.nvm
                    mkdir -p $NVM_DIR
                    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
                    . $NVM_DIR/nvm.sh
                    nvm install $NODE_VERSION
                    nvm use $NODE_VERSION
                    npm install -g yarn
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    export NVM_DIR=$WORKSPACE/.nvm
                    . $NVM_DIR/nvm.sh
                    nvm use v18.20.2
                    yarn install
                '''
            }
        }

        stage('Create local.properties') {
            steps {
                sh '''
                    echo "sdk.dir=/home/ext_rmadan_vecv_in/android-sdk" > android/local.properties
                    cat android/local.properties
                '''
            }
        }

        stage('Build Android') {
            steps {
                sh '''
                    export NVM_DIR=$WORKSPACE/.nvm
                    . $NVM_DIR/nvm.sh
                    nvm use v18.20.2
                    cd android
                    ./gradlew assembleDebug
                '''
            }
        }
    }
}
