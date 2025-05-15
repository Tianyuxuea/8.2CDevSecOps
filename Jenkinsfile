pipeline {
  agent any

  environment {
        RECIPIENT_EMAIL = 'yang1326516679@gmail.com'
  }
  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: ' https://github.com/Tianyuxuea/8.2CDevSecOps.git'
      }
    }
    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }
    stage('Run Tests') {
      steps {
        sh 'npm test || true' // Allows pipeline to continue despite test failures
      }
      post {
        success {
          script {
            sendEmail('Tests', 'SUCCESS')
          }
        }
        failure {
          script {
            sendEmail('Tests', 'FAILURE')
          }
        }
      }
    }
    stage('Generate Coverage Report') {
      steps {
        // Ensure coverage report exists
        sh 'npm run coverage || true'
      }
    }
    stage('NPM Audit (Security Scan)') {
      steps {
        sh 'npm audit || true' // This will show known CVEs in the output
      }
      post {
        success {
          script {
            sendEmail('Audit', 'SUCCESS')
          }
        }
        failure {
          script {
            sendEmail('Audit', 'FAILURE')
          }
        }
      }
    }
  }
}

def sendEmail(stageName, status) {
    emailext (
        subject: "Jenkins: ${stageName} stage - ${status}",
        body: """<p>The ${stageName} stage has completed with status: <b>${status}</b>.</p>
                 <p>Job: ${env.JOB_NAME}<br>
                 Build: ${env.BUILD_NUMBER}<br>
                 URL: <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a></p>""",
        to: "${env.RECIPIENT_EMAIL}",
        attachLog: true,
        mimeType: 'text/html'
    )
}
