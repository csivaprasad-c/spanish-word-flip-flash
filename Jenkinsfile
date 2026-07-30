pipeline {
    agent {
        kubernetes {
            label 'jenkins-agent'
            defaultContainer "jnlp"
            yaml """
apiVersion: v1
kind: Pod
spec:
    containers:
    - name: jnlp
      image: jenkins/inbound-agent:latest
    - name: node-builder
      image: node:22-alpine
      command: ["cat"]
      tty: true
    - name: playwright-runner
      image: mcr.microsoft.com/playwright:v1.54.2-noble
      command: ["cat"]
      tty: true
"""
        }
    }

    options {
        ansiColor('xterm')
    }

    stages {
        stage("build") {
            steps {
                container("node-builder") {
                    sh "npm ci --include=dev"
                    sh "npm run build"
                }
            }
        }

        stage("Unit Tests") {
            steps {
                container("node-builder") {
                    sh "npx vitest run --reporter=verbose"
                }
            }
        }
        stage("Playwright E2E Tests") {
            steps {
                container("playwright-runner") {
                    sh 'CI=true npx playwright test'
                }
            }
        }


        stage("e2e") {
            environment {
                E2E_BASE_URL = 'https://spanish-cards.netlify.app/'
            }
            steps {
                container("playwright-runner") {
                    sh 'CI=true npx playwright test'
                }
            }
            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: true, icon: '', keepAll: false, reportDir: 'reports-e2e/html/', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                    junit stdioRetention: 'ALL', testResults: 'reports-e2e/junit.xml'
                }
            }
        }
    }
}
