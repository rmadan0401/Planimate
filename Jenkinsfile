pipeline {
  agent any

  environment {
    NVM_DIR = '/home/ext_rmadan_vecv_in/.nvm'
  }

  stages {
    stage('Checkout') {
      steps {
        git credentialsId: 'github-credentials', url: 'https://github.com/rmadan0401/Planimate.git', branch: 'main'
      }
    }
    
    stage('Install Node & Yarn') {
      steps {
        sh '''
          export NVM_DIR="$NVM_DIR"
          [ -s "$NVM_DIR/nvm.sh" ] && \\. "$NVM_DIR/nvm.sh"
          nvm use 18.20.7
          node -v
          npm -v
          yarn -v || npm install -g yarn
        '''
      }
    }

    stage('Install Dependencies') {
      steps {
        sh '''
          export NVM_DIR="$NVM_DIR"
          [ -s "$NVM_DIR/nvm.sh" ] && \\. "$NVM_DIR/nvm.sh"
          nvm use 18.20.7
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
          yarn android:build  // Replace with your actual build command
        '''
      }
    }
  }
}
