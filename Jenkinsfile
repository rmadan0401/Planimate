pipeline {
    agent any

    environment {
        NODE_VERSION = "18.20.7"
        YARN_VERSION = "3.6.4"
        NODE_DIR = "${WORKSPACE}/.node"
        PATH = "${NODE_DIR}/bin:${PATH}"
        ANDROID_HOME = "/path/to/android/sdk" // Replace with actual Android SDK path if available
        PATH = "${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/cmdline-tools/latest/bin:${PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/rmadan0401/Planimate.git', credentialsId: 'github-credentials'
            }
        }

        stage('Install Node & Yarn Locally') {
            steps {
                sh '''
                    mkdir -p ${NODE_DIR}
                    curl -o node.tar.xz https://nodejs.org/dist/v18.20.7/node-v18.20.7-linux-x64.tar.xz
                    tar -xf node.tar.xz --strip-components=1 -C ${NODE_DIR}
                    rm node.tar.xz

                    ${NODE_DIR}/bin/node -v
                    ${NODE_DIR}/bin/npm -v

                    corepack enable
                    corepack prepare yarn@${YARN_VERSION} --activate
                    yarn -v
                '''
            }
        }

        stage('Yarn Install') {
            steps {
                sh 'yarn install'
            }
        }

        stage('Fix gradlew Permissions') {
            steps {
                sh 'chmod +x android/gradlew'
            }
        }

        stage('Build Android APK') {
            steps {
                sh '''
                    cd android
                    ./gradlew assembleDebug
                '''
            }
        }
    }

    post {
        always {
            echo "Build completed. Cleaning up if needed."
        }
    }
}
