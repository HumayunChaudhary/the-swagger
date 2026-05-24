ubuntu@ip-11-0-0-23:~$ cat the-swagger/Jenkinsfile 
pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = credentials('aws-account-id')
        AWS_REGION = "us-east-1"
        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
        DATABASE_URL = credentials('database_url')
    }
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

        stage ('Login to ECR') {
            steps {
                sh 'aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REGISTRY'
            }
        }

        stage ('Push Images to ECR') {
            steps {
                pushImage('swagger-web')
                pushImage('swagger-api')
            }
        }

   /*  stage ('Verify RDS connectivity') {
            steps {
                sh '''
                    docker run --network the-swagger-net \
                    --rm $ECR_REGISTRY/the-swagger:swagger-api-$GIT_COMMIT \
                    sh -c 'nc -zv the-swagger-rds-identifier.ci7k4622k2cg.us-east-1.rds.amazonaws.com 5432'
                '''
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
                        -e DATABASE_URL="$DATABASE_URL" \
                        -e JWT_SECRET="your-super-secret-jwt-key-change-in-production" \
                        -e JWT_EXPIRES_IN="7d" \
                        -e PORT=4200 \
                        -e NODE_ENV=production \
                        -e COOKIE_SECRET="your-cookie-secret-change-in-production" \
                        -e CORS_ORIGIN="http://3.87.171.214:4000" \
                        $ECR_REGISTRY/the-swagger:swagger-api-$GIT_COMMIT

                    docker run -d \
                        --name swagger-web \
                        --network the-swagger-net \
                        -p 4000:4000 \
                        -e API_BASE_URL=http://the-swagger-api:4200 \
                        $ECR_REGISTRY/the-swagger:swagger-web-$GIT_COMMIT
                '''
            }
        }  */
    }
}

def buildImage (imageName, dockerfile) {
    sh "docker build -f ${dockerfile} -t ${ECR_REGISTRY}/the-swagger:${imageName}-${GIT_COMMIT} ."
}

def pushImage (imageName) {
    sh "docker push ${ECR_REGISTRY}/the-swagger:${imageName}-${GIT_COMMIT}"
}
