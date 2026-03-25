pipeline {
  agent any
  environment { BASE_URL = credentials('BASE_URL') }
  options { timeout(60); timestamps() }

  stages {
    stage('Checkout')         { steps { checkout scm } }
    stage('Install deps')     { steps { bat 'npm ci' } }
    stage('Install browsers') { steps { bat 'npx playwright install' } }
    stage('Run tests')        { steps { bat 'npx playwright test --reporter=html,line,junit' } }
  }

  post {
    always {
      archiveArtifacts artifacts: 'playwright-report/**', fingerprint: true

      publishHTML(target: [
        reportDir            : 'playwright-report',
        reportFiles          : 'index.html',
        reportName           : 'Playwright Report',
        keepAll              : true,
        allowMissing         : false,
        alwaysLinkToLastBuild: true
      ])

      junit allowEmptyResults: true,
            testResults      : 'test-results/*.xml'
    }
  }
}
