pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = 'azamdevops'
        DOCKERHUB_CREDENTIALS_ID = 'dockerhub-token'
    }

    stages {
        // The 'Checkout' stage has been removed. Jenkins does this automatically.

        stage('Test Backend') {
            steps {
                script {
                    dir('backend') {
                        docker.image('python:3.10-slim').inside {
                            sh 'pip install -r requirements.txt'
                            sh 'pip install flake8'
                            sh 'flake8 .'
                        }
                    }
                }
            }
        }

        stage('Test Frontend') {
            steps {
                script {
                    dir('frontend') {
                        docker.image('node:18-alpine').inside {
                            sh 'npm ci'
                            sh 'npx eslint .'
                        }
                    }
                }
            }
        }

        stage('Build & Push Backend') {
            steps {
                script {
                    dir('backend') {
                        def backendImage = docker.build("${DOCKERHUB_USERNAME}/auth-backend:latest")
                        docker.withRegistry('https://registry.hub.docker.com', DOCKERHUB_CREDENTIALS_ID) {
                            backendImage.push()
                        }
                    }
                }
            }
        }

        stage('Build & Push Frontend') {
            steps {
                script {
                    dir('frontend') {
                        def frontendImage = docker.build("${DOCKERHUB_USERNAME}/auth-frontend:latest")
                        docker.withRegistry('https://registry.hub.docker.com', DOCKERHUB_CREDENTIALS_ID) {
                            frontendImage.push()
                        }
                    }
                }
            }
        }
    }
}