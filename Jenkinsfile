pipeline {
  agent any
  environment {
    NODE_VERSION = "18.20.7"
  }
  stages {
    stage('Clone Repo') {
      steps {
        git branch: 'main', url: 'https://github.com/wfarat/Planimate.git', credentialsId: 'github-credentials'
      }
    }

    stage('Setup Node and Yarn Locally') {
      steps {
        sh '''
          export NVM_DIR=$WORKSPACE/.nvm
          mkdir -p $NVM_DIR
          curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
          . $NVM_DIR/nvm.sh
          nvm install ${NODE_VERSION}
          nvm use ${NODE_VERSION}
          npm install -g yarn@3.6.4
          echo ". $NVM_DIR/nvm.sh && nvm use ${NODE_VERSION}" > $WORKSPACE/.nvmrc
        '''
      }
    }

    stage('Install Dependencies') {
      steps {
        sh '''
          . $WORKSPACE/.nvm/nvm.sh && nvm use ${NODE_VERSION}
          yarn install --network-timeout 100000
        '''
      }
    }

    stage('Start Metro Bundler') {
      steps {
        sh '''
          . $WORKSPACE/.nvm/nvm.sh && nvm use ${NODE_VERSION}
          nohup yarn start > metro.log 2>&1 &
        '''
      }
    }

    stage('Build Android') {
      steps {
        sh '''
          . $WORKSPACE/.nvm/nvm.sh && nvm use ${NODE_VERSION}
          yarn android
        '''
      }
    }
  }
}
