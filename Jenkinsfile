pipeline {
    agent any
    
    options {
        // Clean workspace before build
        skipDefaultCheckout(false)
        timestamps()
    }
    
    environment {
        // Docker Hub credentials ID (configured in Jenkins)
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        
        // Docker Hub username and image details
        DOCKER_USERNAME = 'ahmeddiab23'
        IMAGE_NAME = 'nginx-custom'
        IMAGE_TAG = '1.0'
        DOCKER_IMAGE = "${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
        
        // AWS EC2 details - Replace with your actual EC2 IP address
        EC2_HOST = '100.26.51.207'        // Example: '3.145.67.89' (your EC2 public IP)
        EC2_USER = 'ubuntu'            // Use 'ubuntu' for Ubuntu AMIs
        EC2_CREDENTIALS_ID = 'ec2-ssh-credentials'
    }
    
    stages {
        stage('Clean Workspace') {
            steps {
                echo 'Cleaning workspace...'
                cleanWs()
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: ${DOCKER_IMAGE}"
                echo "Verifying files in workspace:"
                sh 'ls -la'
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}")
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                echo 'Logging in to Docker Hub and pushing image...'
                script {
                    docker.withRegistry('https://registry.hub.docker.com', "${DOCKER_CREDENTIALS_ID}") {
                        dockerImage.push()
                        dockerImage.push('latest')  // Also tag as latest
                    }
                }
            }
        }
        
        stage('Deploy to EC2') {
            steps {
                echo 'Deploying to AWS EC2 instance...'
                script {
                    sshagent(credentials: ["${EC2_CREDENTIALS_ID}"]) {
                        sh """
                            ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} '
                                # Stop and remove existing container if running
                                docker stop nginx-app || true
                                docker rm nginx-app || true
                                
                                # Pull the latest image
                                docker pull ${DOCKER_IMAGE}
                                
                                # Run the new container
                                docker run -d -p 80:80 --name nginx-app --restart always ${DOCKER_IMAGE}
                                
                                # Clean up old images
                                docker image prune -f
                            '
                        """
                    }
                }
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully! 🎉'
            echo "Application deployed at: http://${EC2_HOST}"
        }
        failure {
            echo 'Pipeline failed! ❌'
        }
        always {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
    }
}