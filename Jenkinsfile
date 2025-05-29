node {
  stage('Checkout') {
    // grab your code
    git url: 'https://github.com/Tanvir-ctrl1/8.2CDevSecOps.git', branch: 'main'
  }

  // now run everything inside node:14-alpine
  docker.image('node:14-alpine').inside('-u root:root') {
    stage('Validate Docker Image') {
      sh '''
        echo "Running in Docker:"
        node --version
        npm --version
      '''
    }

    stage('Install Dependencies') {
      sh 'npm ci'
    }

    stage('Run Tests') {
      sh 'npm test || true'
    }

    stage('Generate Coverage') {
      sh 'npm run coverage || true'
      archiveArtifacts artifacts: 'coverage/**', fingerprint: true
    }

    stage('Security Scan') {
      sh 'npm audit --json > npm-audit.json || true'
      archiveArtifacts artifacts: 'npm-audit.json', allowEmptyArchive: true
    }
  }

  // final notifications
  stage('Done') {
    // if you want, you can inspect current build result:
    echo currentBuild.currentResult == 'SUCCESS' 
      ? '✅ Pipeline completed successfully.' 
      : '❌ Pipeline failed – check console output.'
  }
}
