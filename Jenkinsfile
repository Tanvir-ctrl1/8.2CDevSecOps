pipeline {
  agent any

  environment {
    SNYK_TOKEN = credentials('SNYK_TOKEN')
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main',
            url: 'https://github.com/Tanvir-ctrl1/8.2CDevSecOps.git'
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
        always { junit '**/test-results/*.xml' }
      }
    }

    stage('Generate Coverage Report') {
      steps {
        sh 'npm run coverage || true'
      }
      post {
        always { archiveArtifacts artifacts: 'coverage/**', fingerprint: true }
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        sh 'npm audit --audit-level=low || true'
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: '**/npm-debug.log', allowEmptyArchive: true
    }
    success {
      echo '🎉 Security scan complete — review the console logs for audit details.'
    }
    failure {
      mail to: 'you@example.com',
           subject: "Build ${currentBuild.fullDisplayName} Failed",
           body: "Check the Jenkins console output at ${env.BUILD_URL}"
    }
  }
}
