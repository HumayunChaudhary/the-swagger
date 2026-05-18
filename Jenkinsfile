pipeline {
    agent any
    stages {
        stage ('Build Images') {
            parallel {
                stage ('Build swagger-db') {
                    steps {
                        sh '''
                            buildImage('swagger-db', 'Dockerfile.db')
                        '''
                    }
                }
                stage ('Build swagger-api') {
                    steps {
                        sh '''
                            buildImage('swagger-api', 'Dockerfile.api')
                        '''
                    }
                }
                stage ('Build swagger-web') {
                    steps {
                        sh '''
                            buildImage('swagger-web', 'Dockerfile.web')
                        '''
                    }                
                }
            }
        }
    }
}

def buildImage (imageName, dockerfile) {
    sh "docker build -f ${dockerfile} -t {imageName}:${GIT_COMMIT} ."
}
