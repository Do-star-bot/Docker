pipeline {
    agent any

    environment {
        // Change 'your-dockerhub-username' to your actual Docker Hub ID
        DOCKER_IMAGE = "your-dockerhub-username/flask-app:${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                // This pulls your code from GitHub
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                script {
                    // Builds the image using the Dockerfile you provided
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    // Stops any existing container and runs the new one
                    sh "docker stop flask-container || true"
                    sh "docker rm flask-container || true"
                    sh "docker run -d -p 5000:5000 --name flask-container ${DOCKER_IMAGE}"
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution finished.'
        }
    }
}
