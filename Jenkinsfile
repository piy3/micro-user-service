pipeline {
    agent any
    
    triggers {
        githubPush()
    }
    
    environment {
        AWS_ACCOUNT_ID = "568035148820" 
        AWS_REGION     = "eu-north-1"    
        IMAGE_NAME     = "user-service-app"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${IMAGE_NAME}"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/piy3/micro-user-service'
            }
        }
        stage('ECR Login') {
            steps {
                sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
            }
        }
        stage('Build Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
                sh "docker tag ${IMAGE_NAME}:latest ${REPOSITORY_URI}:latest"
            }
        }
        stage('Push to ECR') {
            steps {
                sh "docker push ${REPOSITORY_URI}:latest"
            }
            post {
                success {
                    // sh "docker rmi ${IMAGE_NAME}:latest || true"
                    // sh "docker rmi ${REPOSITORY_URI}:latest || true"
                    sh "docker image prune -f"
                }
            }
        }
    }
}