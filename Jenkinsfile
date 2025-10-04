pipeline {
    agent any
    
    environment {
        // Docker Hub credentials (configured in Jenkins)
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        
        // Docker Hub username and image details
        DOCKER_USERNAME = 'ahmeddiab23'
        IMAGE_NAME = 'nginx-custom'
        IMAGE_TAG = '1.0'
        DOCKER_IMAGE = "${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
        
        // AWS EC2 details 
        EC2_HOST = '100.26.51.207'     
        EC2_USER = 'ubuntu'                
        EC2_CREDENTIALS_ID = 'ec2-ssh-credentials'
    }
    
    stages {
        stage('Verify Workspace') {
            steps {
                echo '========== Verifying Workspace =========='
                sh 'pwd'
                sh 'ls -la'
                sh 'echo "--- Dockerfile ---"'
                sh 'cat Dockerfile'
                sh 'echo "--- index.html ---"'
                sh 'cat index.html'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo "========== Building Docker Image: ${DOCKER_IMAGE} =========="
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}")
                }
                echo ' Docker image built successfully!'
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                echo '========== Pushing to Docker Hub =========='
                script {
                    docker.withRegistry('https://registry.hub.docker.com', "${DOCKER_CREDENTIALS_ID}") {
                        dockerImage.push("${IMAGE_TAG}")
                        dockerImage.push('latest')
                    }
                }
                echo ' Image pushed to Docker Hub successfully!'
            }
        }
        
        stage('Deploy to EC2') {
            steps {
                echo '========== Deploying to AWS EC2 =========='
                script {
                    sshagent(credentials: ["${EC2_CREDENTIALS_ID}"]) {
                        sh """
                            ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} '
                                echo "Stopping existing container..."
                                docker stop nginx-app || true
                                docker rm nginx-app || true
                                
                                echo "Pulling latest image..."
                                docker pull ${DOCKER_IMAGE}
                                
                                echo "Starting new container..."
                                docker run -d -p 80:80 --name nginx-app --restart always ${DOCKER_IMAGE}
                                
                                echo "Cleaning up old images..."
                                docker image prune -f
                                
                                echo "Deployment complete!"
                                docker ps | grep nginx-app
                            '
                        """
                    }
                }
                echo ' Deployed to EC2 successfully!'
            }
        }
    }
    
    post {
        success {
            echo '━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━'
            echo ' PIPELINE COMPLETED SUCCESSFULLY! '
            echo '━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━'
            echo " Application URL: http://${EC2_HOST}"
            echo " Docker Hub: https://hub.docker.com/r/${DOCKER_USERNAME}/${IMAGE_NAME}"
            echo '━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━'
        }
        failure {
            echo '━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━'
            echo ' PIPELINE FAILED!'
            echo '━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━'
        }
        always {
            echo 'Pipeline execution finished.'
        }
    }
}