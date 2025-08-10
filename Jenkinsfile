pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = 'azamdevops'
        DOCKERHUB_CREDENTIALS_ID = 'dockerhub-token'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/mdazamdevops/signup-app-flask-jenkins.git'
            }
        }

        stage('Test Backend') {
            steps {
                script {
                    dir('backend') {
                        docker.image('python:3.10-slim').inside {
                            sh 'pip install --no-cache-dir --user -r requirements.txt'
                            sh 'pip install --no-cache-dir --user flake8'
                            sh 'flake8 . || true'
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
                            sh 'npx eslint . || true'
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
