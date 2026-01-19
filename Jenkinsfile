pipeline {
    agent any

    environment {
        AWS_REGION    = "ap-south-1"
        AWS_ACCOUNT   = "463850984755"
        ECR_REPO      = "reddit-app"
        ECR_REGISTRY  = "463850984755.dkr.ecr.ap-south-1.amazonaws.com"
        IMAGE_TAG     = "${BUILD_NUMBER}"
        IMAGE_NAME    = "${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'git@github.com:sumitgaikwad08/reddit-app.git'
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                echo "Building Docker image..."
                docker build -t ${ECR_REPO}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Authenticate to ECR') {
            steps {
                sh '''
                echo "Logging in to Amazon ECR..."
                aws ecr get-login-password --region ${AWS_REGION} | \
                docker login --username AWS --password-stdin ${ECR_REGISTRY}
                '''
            }
        }

        stage('Tag Image') {
            steps {
                sh '''
                echo "Tagging image for ECR..."
                docker tag ${ECR_REPO}:${IMAGE_TAG} ${IMAGE_NAME}
                '''
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh '''
                echo "Pushing image to ECR..."
                docker push ${IMAGE_NAME}
                '''
            }
        }
    }

    post {
        success {
            echo "CI Pipeline completed successfully!"
            echo "Image pushed: ${IMAGE_NAME}"
        }

        failure {
            echo "CI Pipeline FAILED"
        }

        always {
            sh '''
            echo "Cleaning up local Docker images..."
            docker image prune -f
            '''
        }
    }
}

