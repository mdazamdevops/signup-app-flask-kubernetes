// Jenkinsfile (Declarative Pipeline)

pipeline {
    // 1. Agent Configuration
    // Specifies that the pipeline can run on any available Jenkins agent.
    // The agent must have Docker and Git installed.
    agent any

    // 2. Environment Variables
    // Defines variables used throughout the pipeline for consistency.
    environment {
        // Replace 'my-flask-app' with your desired application name
        APP_NAME = 'my-flask-app'
        // Use the Jenkins build number for unique image tagging
        IMAGE_TAG = "build-${env.BUILD_NUMBER}"
    }

    // 3. Pipeline Stages
    // The pipeline is broken down into logical stages.
    stages {
        // Stage 1: Checkout code from your repository
        stage('Checkout') {
            steps {
                // Clones the repository code to the Jenkins workspace
                checkout scm
                echo "Code checked out successfully."
            }
        }

        // Stage 2: Build a Docker image for the application
        stage('Build') {
            steps {
                script {
                    echo "Building Docker image: ${APP_NAME}:${IMAGE_TAG}"
                    // The 'docker build' command creates an image using the Dockerfile
                    sh "docker build -t ${APP_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        // Stage 3: Run placeholder tests
        stage('Test') {
            steps {
                // This is a placeholder for your actual test suite.
                // In a real-world scenario, you would run unit tests (e.g., pytest)
                // or other automated quality checks here.
                echo "Running placeholder tests..."
                sh "echo 'Tests passed!'"
            }
        }

        // Stage 4: Deploy the application as a Docker container
        stage('Deploy') {
            steps {
                script {
                    echo "Deploying application..."

                    // This block ensures that if a container with the same name exists,
                    // it is stopped and removed before launching the new one.
                    // The '|| true' prevents the pipeline from failing if the container doesn't exist.
                    sh "docker stop ${APP_NAME} || true"
                    sh "docker rm ${APP_NAME} || true"

                    echo "Starting new container..."
                    // The 'docker run' command starts a new container.
                    // -d: detached mode (runs in the background)
                    // --name: assigns a name to the container
                    // -p: maps port 8000 on the host to port 5000 in the container
                    sh "docker run -d --name ${APP_NAME} -p 8000:5000 ${APP_NAME}:${IMAGE_TAG}"

                    echo "Deployment successful. App is running on http://<your-server-ip>:8000"
                }
            }
        }
    }

    // 4. Post-build Actions
    // Actions that run at the end of the pipeline, regardless of its status.
    post {
        always {
            // Cleans up the Jenkins workspace to save disk space.
            cleanWs()
            echo "Pipeline finished. Workspace cleaned."
        }
    }
}