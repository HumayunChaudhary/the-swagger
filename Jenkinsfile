pipeline {
    agent any
    stages {
        stage ('Build Images') {
            parallel {
                stage ('Build swagger-db') {
                    steps {
                            buildImage('swagger-db', 'Dockerfile.db')
                    }
                }
                stage ('Build swagger-api') {
                    steps {
                            buildImage('swagger-api', 'Dockerfile.api')
                    }
                }
                stage ('Build swagger-web') {
                    steps {
                            buildImage('swagger-web', 'Dockerfile.web')
                    }                
                }
            }
        }
    }
}

def buildImage (imageName, dockerfile) {
    sh "docker build -f ${dockerfile} -t ${imageName}:${GIT_COMMIT} ."
}
