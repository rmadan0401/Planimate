pipeline {
  agent any

  environment {
    NVM_DIR = "/home/ext_rmadan_vecv_in/.nvm"
    NODE_VERSION = "18.19.1"
  }

  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/rmadan0401/Planimate.git', branch: 'main', credentialsId: 'github-credentials'
      }
    }
    
    stage('Setup Node & Yarn') {
      steps {
        sh '''
          export NVM_DIR=$NVM_DIR
          [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
          nvm install $NODE_VERSION
          nvm use $NODE_VERSION
          node -v
          npm install -g yarn
          yarn set version 3.6.4
          yarn -v
        '''
      }
    }

    stage('Install Dependencies') {
      steps {
        sh '''
          export NVM_DIR=$NVM_DIR
          [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
          nvm use $NODE_VERSION
          yarn install
        '''
      }
    }

    stage('Build Android') {
      steps {
        sh '''
          export NVM_DIR=$NVM_DIR
          [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
          nvm use $NODE_VERSION
          cd android
          ./gradlew assembleDebug
        '''
      }
    }
  }
}
