pipeline {
    agent any

    options {
        ansiColor('xterm')
    }

    stages {
        stage("build") {
            agent {
                docker {
                    image "node:22-alpine"
                }
            }
            steps {
                sh "npm ci"
                sh "npm run build"
            }
        }
    }
}