pipeline {
    agent any

    environment {
        // Your Docker Hub details
        DOCKER_USER = "Do-star-bot"
        IMAGE_NAME  = "Docker"
        
        // This ID MUST match what you name the credential in Jenkins
        REGISTRY_CREDENTIALS_ID = 'docker-hub-creds'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Pulling code from GitHub...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building Docker Image...'
                // Build and tag the image using your Docker Hub username
                sh "docker build -t ${DOCKER_USER}/${IMAGE_NAME}:latest ."
            }
        }

        stage('Test') {
            steps {
                echo 'Running Python Syntax Check...'
                // Simple syntax check for Python
                sh "python3 -m py_compile app.py"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // Logic: Uses the stored credentials to log in, push, and log out automatically
                    docker.withRegistry('', "${REGISTRY_CREDENTIALS_ID}") {
                        echo 'Pushing Image to Docker Hub...'
                        sh "docker push ${DOCKER_USER}/${IMAGE_NAME}:latest"
                    }
                }
            }
        }

        stage('Deploy Local') {
            steps {
                echo ' Refreshing Local Container...'
                // Stop and remove old container if it exists, then run the new one
                sh "docker stop ${IMAGE_NAME}_container || true"
                sh "docker rm ${IMAGE_NAME}_container || true"
                sh "docker run -d -p 5000:5000 --name ${IMAGE_NAME}_container ${DOCKER_USER}/${IMAGE_NAME}:latest"
            }
        }
    }

    post {
        success {
            echo 'Success! Your app is live and the image is on Docker Hub.'
        }
        failure {
            echo 'Pipeline Failed. Check the Console Output for errors.'
        }
    }
}
