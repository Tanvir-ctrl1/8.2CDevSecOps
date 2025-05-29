pipeline {
  agent {
    docker {
      image 'node:14-alpine'
      label ''            // optional: limit to Docker-capable agents
      args '-u root:root' // optional: run as root for caching/installs
    }
  }

  tools {
    nodejs 'NodeJS_14'    // match this name in Manage Jenkins → Global Tool Config
  }

  stages {
    stage('Validate Workspace') {
      steps {
        echo "Running inside Docker container:"
        sh 'node --version && npm --version'
      }
    }

    stage('Checkout') {
      steps {
        git url: 'https://github.com/Tanvir-ctrl1/8.2CDevSecOps.git', branch: 'main'
      }
    }

    stage('Install Dependencies') {
      steps {
        withEnv(["PATH+NODE=${tool 'NodeJS_14'}/bin"]) {
          sh 'npm install'
        }
      }
    }

    stage('Run Tests') {
      steps {
        withEnv(["PATH+NODE=${tool 'NodeJS_14'}/bin"]) {
          sh 'npm test || true'
        }
      }
    }

    stage('Generate Coverage') {
      steps {
        withEnv(["PATH+NODE=${tool 'NodeJS_14'}/bin"]) {
          sh 'npm run coverage || true'
        }
      }
      post {
        always {
          archiveArtifacts artifacts: 'coverage/**', fingerprint: true
        }
      }
    }

    stage('Security Scan') {
      steps {
        withEnv(["PATH+NODE=${tool 'NodeJS_14'}/bin"]) {
          sh 'npm audit --json > npm-audit.json || true'
        }
      }
      post {
        always {
          archiveArtifacts artifacts: 'npm-audit.json', allowEmptyArchive: true
        }
      }
    }
  }

  post {
    success { echo '✅ Pipeline completed successfully.' }
    failure { echo '❌ Pipeline failed – check console output.' }
  }
}
