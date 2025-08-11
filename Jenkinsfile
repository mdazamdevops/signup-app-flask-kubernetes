// Jenkinsfile for a Build, Push, and Deploy workflow

pipeline {
    agent any

    environment {
        DOCKER_REGISTRY_USER = 'azamdevops'
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    echo "Building backend and frontend images..."
                    sh "docker build -t ${env.DOCKER_REGISTRY_USER}/auth-backend:latest ./backend"
                    sh "docker build -t ${env.DOCKER_REGISTRY_USER}/auth-frontend:latest ./frontend"
                }
            }
        }

        stage('Push to Docker Registry') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        echo "Logging in and pushing images..."
                        sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}"
                        sh "docker push ${env.DOCKER_REGISTRY_USER}/auth-backend:latest"
                        sh "docker push ${env.DOCKER_REGISTRY_USER}/auth-frontend:latest"
                    }
                }
            }
        }

        // --- MODIFIED DEPLOY STAGE ---
        stage('Deploy from Registry') {
            steps {
                script {
                    echo "Deploying application using docker-compose.prod.yml..."
                    
                    // Use the -f flag to specify the production compose file for each command
                    sh "docker-compose -f docker-compose.prod.yml pull"
                    sh "docker-compose -f docker-compose.prod.yml down"
                    sh "docker-compose -f docker-compose.prod.yml up -d"

                    echo "✅ Deployment successful!"
                    echo "Application is running with the latest production images."
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout'
            cleanWs()
            echo "Pipeline finished."
        }
    }
}