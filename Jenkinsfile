pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = 'azamdevops'
        DOCKERHUB_CREDENTIALS_ID = 'dockerhub-token'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/mdazamdevops/signup-app-flask-jenkins.git'
            }
        }

        stage('Test Backend') {
            steps {
                dir('backend') {
                    script {
                        docker.image('python:3.10-slim').inside {
                            sh '''
                                python -m pip install --upgrade pip
                                pip install --no-cache-dir --break-system-packages -r requirements.txt
                                pip install --no-cache-dir --break-system-packages flake8
                                flake8 .
                            '''
                        }
                    }
                }
            }
        }

        stage('Test Frontend') {
            steps {
                dir('frontend') {
                    script {
                        docker.image('node:18-alpine').inside {
                            sh '''
                                npm ci
                                npx eslint . || true
                            '''
                        }
                    }
                }
            }
        }

        stage('Build & Push Backend') {
            steps {
                dir('backend') {
                    script {
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
                dir('frontend') {
                    script {
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
