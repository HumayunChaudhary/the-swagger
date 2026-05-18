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
        stage ('Deploy on EC2') {
            steps {
                sh '''
                    docker network inspect the-swagger-net || docker network create the-swagger-net
                    docker run -d \
                        --name the-swagger-db \
                        --network the-swagger-net \
                        -v postgres_data:/var/lib/postgresql/data \
                        swagger-db:latest
                    docker run -d \
                        --name the-swagger-api \
                        --network the-swagger-net \
                        -e DATABASE_URL="postgresql://postgres:postgres@the-swagger-db:5432/the_swagger_dev?schema=public" \
                        -e JWT_SECRET="your-super-secret-jwt-key-change-in-production" \
                        -e JWT_EXPIRES_IN="7d" \
                        -e PORT=4200 \
                        -e NODE_ENV=production \
                        -e COOKIE_SECRET="your-cookie-secret-change-in-production" \
                        -e CORS_ORIGIN="http://54.234.69.172:4000" \
                        swagger-api:latest 

                    docker run -d \
                        --name swagger-web \
                        --network the-swagger-net \
                        -p 4000:4000 \
                        -e API_BASE_URL=http://the-swagger-api:4200 \
                        swagger-web:latest
                '''
            }
        }
    }
}

def buildImage (imageName, dockerfile) {
    sh "docker build -f ${dockerfile} -t ${imageName}:${GIT_COMMIT} ."
}
