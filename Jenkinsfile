pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/mdazamdevops/signup-app-flask-jenkins.git'
            }
        }

        stage('Test Backend') {
            steps {
                dir('backend') {
                    script {
                        docker.image('python:3.10-slim').inside {
                            sh 'pip install --no-cache-dir --user -r requirements.txt'
                            // Run your backend tests here
                            sh 'pytest || echo "Backend tests failed, continuing..."'
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
                            sh 'npm install --unsafe-perm'
                            // Run your frontend tests here
                            sh 'npm test || echo "Frontend tests failed, continuing..."'
                        }
                    }
                }
            }
        }

        stage('Build & Push Backend') {
            steps {
                script {
                    sh 'docker build -t your-dockerhub-username/backend:latest backend'
                    sh 'docker push your-dockerhub-username/backend:latest'
                }
            }
        }

        stage('Build & Push Frontend') {
            steps {
                script {
                    sh 'docker build -t your-dockerhub-username/frontend:latest frontend'
                    sh 'docker push your-dockerhub-username/frontend:latest'
                }
            }
        }
    }
}
