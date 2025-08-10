pipeline {
    agent any

    environment {
        // Define your Docker Hub username as an environment variable
        DOCKERHUB_USERNAME = 'azamdevops'
        // Use the Jenkins Credentials ID for your Docker Hub token
        DOCKERHUB_CREDENTIALS_ID = 'dockerhub-token'
    }

    stages {
        stage('Checkout') {
            steps {
                // Clones the repository code
                git 'https://github.com/mdazamdevops/signup-app-flask-gh-actions.git'
            }
        }

        stage('Test Backend') {
            steps {
                dir('backend') {
                    // Runs the backend tests inside a temporary Python container
                    docker.image('python:3.10-slim').inside {
                        sh 'pip install -r requirements.txt'
                        sh 'pip install flake8'
                        sh 'flake8 .'
                    }
                }
            }
        }

        stage('Test Frontend') {
            steps {
                dir('frontend') {
                    // Runs the frontend tests inside a temporary Node.js container
                    docker.image('node:18-alpine').inside {
                        sh 'npm ci'
                        sh 'npx eslint .'
                    }
                }
            }
        }

        stage('Build & Push Backend') {
            steps {
                dir('backend') {
                    // Builds the backend Docker image
                    def backendImage = docker.build("${DOCKERHUB_USERNAME}/auth-backend:latest")
                    
                    // Pushes the image to Docker Hub using stored credentials
                    docker.withRegistry('https://registry.hub.docker.com', DOCKERHUB_CREDENTIALS_ID) {
                        backendImage.push()
                    }
                }
            }
        }

        stage('Build & Push Frontend') {
            steps {
                dir('frontend') {
                    // Builds the frontend Docker image
                    def frontendImage = docker.build("${DOCKERHUB_USERNAME}/auth-frontend:latest")

                    // Pushes the image to Docker Hub
                    docker.withRegistry('https://registry.hub.docker.com', DOCKERHUB_CREDENTIALS_ID) {
                        frontendImage.push()
                    }
                }
            }
        }
    }
}