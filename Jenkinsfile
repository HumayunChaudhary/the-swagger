pipeline {
    agent any
    stages {
        stage ('Build Images') {
            parallel {
                stage ('Build swagger-db') {
                    steps {
                        sh 'docker build -f Dockerfile.db -t swagger-db:latest .'
                    }
                }
                stage ('Build swagger-api') {
                    steps {
                        sh 'docker build -f Dockerfile.api -t swagger-api:latest .'
                    }
                }
                stage ('Build swagger-web') {
                    steps {
                        sh 'docker build -f Dockerfile.web -t swagger-web:latest .' 
                    }                
                }
            }
        }
    }
}
