pipeline {
    agent any
    environment {
        NODE_OPTIONS = "--max_old_space_size=4096"
        PATH = "${env.PATH}:/home/ext_rmadan_vecv_in/.yarn/bin:/home/ext_rmadan_vecv_in/.config/yarn/global/node_modules/.bin"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/wfarat/Planimate.git', credentialsId: 'github-credentials'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh '''
                    echo "Node version:"
                    node -v
                    echo "Yarn version:"
                    yarn -v

                    # Clean install with Yarn
                    yarn install --immutable
                '''
            }
        }
        stage('Build Android') {
            steps {
                sh '''
                    # Start Metro bundler in background
                    nohup yarn start &

                    # Give Metro some time to start
                    sleep 10
                    
                    # Build Android
                    yarn android || true  # Android emulator might not be available, avoid failing job
                '''
            }
        }
    }
    post {
        always {
            sh 'pkill -f "node"' // Kill Metro bundler after job
        }
    }
}
