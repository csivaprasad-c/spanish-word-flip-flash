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
      image: ://microsoft.com
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
                    sh "npm ci"
                    sh "npm run build"
                }
            }
        }
        stage("Unit Tests") {
            steps {
                container("node-builder") {
                    sh "npm run test"
                }
            }
        }
    }
}
