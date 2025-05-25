pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/Tanvir-ctrl1/8.2CDevSecOps.git', branch: 'main'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Run Tests') {
      steps {
        sh 'npm test || true'
      }
      post {
        always {
          junit '**/test-results/*.xml'
        }
      }
    }

    stage('Generate Coverage') {
      steps {
        sh 'npm run coverage || true'
      }
      post {
        always {
          archiveArtifacts artifacts: 'coverage/**', fingerprint: true
        }
      }
    }

    stage('Security Scan') {
      steps {
        // Inline JSON audit, no package.json change required
        sh 'npm audit --json > npm-audit.json || true'
      }
      post {
        always {
          archiveArtifacts artifacts: 'npm-audit.json', allowEmptyArchive: true
        }
      }
    }
  }

  post {
    success {
      echo '✅ Pipeline completed successfully.'
    }
    failure {
      echo '❌ Pipeline failed — consult the console output and archived logs.'
    }
  }
}
