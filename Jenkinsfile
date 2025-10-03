pipeline {
    agent any
    
    environment {
        
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        
        
        DOCKER_USERNAME = 'ahmeddiab23'
        IMAGE_NAME = 'nginx-custom'
        IMAGE_TAG = '1.0'
        DOCKER_IMAGE = "${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
       EC2_HOST = '100.26.51.207'
        EC2_USER = 'ubuntu'
        EC2_CREDENTIALS_ID = 'ec2-ssh-credentials'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub...'
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: ${DOCKER_IMAGE}"
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
                        dockerImage.push('latest')  
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
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
        always {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
    }
}