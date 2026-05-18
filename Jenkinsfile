pipeline {
    agent any
    stages {
        stage ('Build Images') {
            parallel {
                stage ('Build swagger-db') {
                    sh 'docker build -f Dockerfile.db -t swagger-db:latest .'

                }
                stage ('Build swagger-api') {
                    sh 'docker build -f Dockerfile.api -t swagger-api:latest .'
                }
                stage ('Build swagger-web') {
                    sh 'docker build -f Dockerfile.web -t swagger-web:latest .'
                }
            }
        }
    }
}
