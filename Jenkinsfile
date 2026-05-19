pipeline {
    agent any
    stages {
        stage ('Build Images') {
            parallel {
         
                stage ('swagger-api') {
                    steps {
                            buildImage('swagger-api', 'Dockerfile.api')
                    }
                }
                stage ('swagger-web') {
                    steps {
                            buildImage('swagger-web', 'Dockerfile.web')
                    }                
                }
            }
        }
        stage ('Deploy on EC2') {
            steps {
                sh '''
                    docker network inspect the-swagger-net > /dev/null || docker network create the-swagger-net
                    docker stop the-swagger-api swagger-web || true
                    docker rm the-swagger-api swagger-web || true
                    docker run -d \
                        --name the-swagger-api \
                        --network the-swagger-net \
			-e DATABASE_URL="postgresql://postgres:postgres234@the-swagger-db.ci7k4622k2cg.us-east-1.rds.amazonaws.com:5432/the_swagger_dev?schema=public"
                        -e JWT_SECRET="your-super-secret-jwt-key-change-in-production" \
                        -e JWT_EXPIRES_IN="7d" \
                        -e PORT=4200 \
                        -e NODE_ENV=production \
                        -e COOKIE_SECRET="your-cookie-secret-change-in-production" \
                        -e CORS_ORIGIN="http://3.87.171.214:4000" \
                        swagger-api:$GIT_COMMIT 

                    docker run -d \
                        --name swagger-web \
                        --network the-swagger-net \
                        -p 4000:4000 \
                        -e API_BASE_URL=http://the-swagger-api:4200 \
                        swagger-web:$GIT_COMMIT
                '''
            }
        }
    }
}

def buildImage (imageName, dockerfile) {
    sh "docker build -f ${dockerfile} -t ${imageName}:${GIT_COMMIT} ."
}
