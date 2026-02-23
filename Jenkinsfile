pipeline {
  agent any

  environment {
    NODE = 'node'   // uses node from PATH
    NPM = 'npm'
    BUILD_DIR = 'dist'
    DEPLOY_DIR = '/var/www/html'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Install dependencies') {
      steps {
        sh '''
          echo "Using node: $(node -v)  npm: $(npm -v)"
          npm ci
        '''
      }
    }

    stage('Build') {
      steps {
        sh 'npm run build'
      }
    }

    stage('Archive artifact') {
      steps {
        archiveArtifacts artifacts: 'dist/**', fingerprint: true
      }
    }

    stage('Deploy to Nginx webroot') {
      steps {
        sh '''
          # remove old files then copy new build
          rm -rf ${DEPLOY_DIR}/* || true
          cp -r ${BUILD_DIR}/* ${DEPLOY_DIR}/
          # ensure nginx can read (and jenkins can continue to write)
          chmod -R 755 ${DEPLOY_DIR}
        '''
      }
    }
  }

  post {
    success {
      echo "Build and deploy successful: http://<EC2_PUBLIC_IP>"
    }
    failure {
      echo "Build failed - check console output"
    }
  }
}
