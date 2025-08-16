# DevOps Internship Task 5 – Build a Kubernetes Cluster Locally with Minikube
### Elevate Labs: Empowering the Future of DevOps
This project is a testament to the high-quality, hands-on learning experience provided by Elevate Labs. Their internship program is dedicated to empowering the next generation of DevOps professionals by offering practical, real-world challenges that build foundational skills and a deep understanding of modern software development practices.


# Simple User Signup & Login Application
### Introduction

Welcome to this full-stack user authentication application. This project was built from the ground up to serve as a practical example of a secure, modern web application. It features a Python backend powered by the Flask framework and a dynamic, user-friendly frontend built with standard HTML, CSS, and JavaScript.

**Project created by Mohd Azam Uddin.**

---
## What is this App?

This application provides a fundamental user authentication system. Its core purpose is to allow new users to create a secure account and for existing users to sign in. It serves as a solid foundation for any web application that requires user management.

**Key Features:**
* Secure user registration with password hashing.
* User login and a personalized welcome dashboard.
* A clean, responsive user interface.

---
## How It Was Built - The Journey

This project started with a simple goal: create a functional signup system. The process evolved as we tackled common development challenges.

#### 1. The Initial Backend
The first version of the application was a simple **Flask** server, storing user data in a `users.json` file. This approach quickly led to an **"Internal Server Error"** due to file permission issues and the risk of data corruption.

#### 2. Upgrading to a Real Database
To fix the errors and make the application more stable, the backend was upgraded to use a proper **SQLite** database. The `Flask-SQLAlchemy` library was used to manage the database, which solved all the data storage problems.

#### 3. Building the Frontend
The user interface was built with standard **HTML**, **CSS**, and **vanilla JavaScript**. A simple **Node.js/Express** server is used to serve the `index.html` file and manage the local development environment.

---
## Containerization with Docker

To ensure the application runs reliably in any environment, it has been fully containerized using Docker.

#### What is Containerization?
Imagine a standardized shipping container that holds not just your application's code, but also the specific version of Python or Node.js it needs, all the required libraries, and the necessary system settings. This "box" can be run on any machine that has Docker installed, guaranteeing that the app will work the same way for a developer, a tester, or in a production environment. This solves the classic "it works on my machine" problem.

#### Creating the Dockerfiles
A `Dockerfile` is a blueprint for building a container image. This project uses two separate `Dockerfile`s for the backend and frontend, which detail the steps to package each service.

---
## Automation with GitHub Actions CI/CD

This project uses a fully automated CI/CD (Continuous Integration/Continuous Deployment) pipeline built with GitHub Actions.

#### What is CI/CD and Why Use It?
CI/CD is a modern software development practice that automates the process of building, testing, and deploying code.
* **Continuous Integration (CI)** automatically tests the code every time a change is pushed. This catches bugs early and ensures that new code doesn't break existing features.
* **Continuous Deployment (CD)** automatically builds the application and deploys it after it passes all the tests.

We use this to save time, reduce human error, and ensure that only high-quality, tested code makes it to production.

#### How the Pipeline Was Built
The entire workflow is defined in a single YAML file located at `.github/workflows/main.yml`. This file contains all the instructions for the automated workflow, which runs on every push to the `main` branch to test, build, and push the Docker images to Docker Hub.

#### Secrets and Variables
To log in to Docker Hub securely, the pipeline uses encrypted secrets stored in the GitHub repository's settings (`Settings > Secrets and variables > Actions`).
* **`DOCKERHUB_USERNAME`**: Stores the Docker Hub username.
* **`DOCKERHUB_TOKEN`**: Stores a secure Docker Hub Access Token used as a password.

---

#### How the Pipeline Was Built
The entire Jenkins workflow is defined in the `Jenkinsfile` located in the project's root directory. This file uses a Declarative Pipeline syntax to define all the stages for building and testing the application.

#### The Pipeline's Workflow: Step-by-Step
When the pipeline is run in Jenkins (either manually or via a webhook), the following stages are executed:

1.  **Checkout**: Jenkins automatically checks out the latest code from the Git repository.
2.  **Test Backend**: This stage runs inside a temporary Docker container (`python:3.10-slim`). It executes the `pip install` command to get dependencies and then runs `flake8 .` to lint the Python code.
3.  **Test Frontend**: This stage runs in a separate `node:18-alpine` container. It uses `npm ci` to install dependencies and `npx eslint .` to check the JavaScript code quality.
4.  **Build & Push Backend**: If all tests pass, this stage uses Docker to build the backend image from its `Dockerfile`.
5.  **Build & Push Frontend**: Finally, this stage builds the frontend image from its `Dockerfile`. Both build stages use `docker.withRegistry()` to securely log in to Docker Hub and push the new images.

#### Secure Credential Management
Instead of GitHub Secrets, Jenkins uses its built-in **Credentials Manager** (`Manage Jenkins > Credentials`). For this pipeline, a "Username with password" credential with the ID `dockerhub-token` was created to securely store the Docker Hub username and access token.

---

## Orchestration with Kubernetes

For a production-grade deployment, this project was deployed to a **Kubernetes** cluster, managed locally with **Minikube**.

#### Why Use Kubernetes?
If Docker puts your app in a standardized box, Kubernetes is the robotic warehouse that manages, organizes, and scales thousands of those boxes automatically. It is the industry standard for running containers in production.
* **High Availability**: It automatically restarts containers that fail, ensuring the application is always running.
* **Scalability**: It can automatically scale the number of running containers up or down based on traffic.
* **Service Discovery & Load Balancing**: Kubernetes has a built-in network that allows containers to easily find and communicate with each other, and it automatically load balances requests between them.

#### Managing Pods and Services
The application is defined in YAML files located in the `k8s/` directory. These files describe the desired state to Kubernetes. The following commands were used to manage the application:
* `kubectl apply -f <file.yaml>`: Deploys or updates the application.
* `kubectl get pods`: Checks the status of the running containers (Pods).
* `kubectl get services`: Checks the network endpoints (Services) for the application.
* `kubectl logs <pod-name>`: Views the logs from a specific container to debug issues.
* `kubectl scale deployment --replicas=3 <deployment-name>`: Scales the number of running pods.

#### Load Balancing with NGINX Ingress
To expose the application to the outside world and manage traffic efficiently, an **NGINX Ingress Controller** was used.
1.  **Enable Ingress**: The controller was first enabled in the Minikube cluster with the command: `minikube addons enable ingress`.
2.  **Define Rules**: An `Ingress` resource was created in a YAML file. This resource acts as a smart traffic cop, defining rules for how requests are routed:
    * Requests to the root path (`/`) are sent to the `frontend-service`.
    * Requests to the API path (`/api/`) are sent to the `backend-service`.
This setup provides a single, stable entry point for the entire application and handles load balancing to the backend pods automatically.

---
## Technologies & Dependencies Used

* **Backend**: Python, Flask, Flask-SQLAlchemy, Flask-CORS, Gunicorn
* **Frontend**: HTML5, CSS3, Vanilla JavaScript, Node.js, Express.js
* **Containerization**: Docker, Docker Compose
* **CI/CD**: GitHub Actions, Jenkins
* **IaC**: Terraform
* **Orchestration**: Kubernetes, Minikube, NGINX Ingress

---
## How to Run Locally

The recommended way to run this application is with Docker Compose, as it handles both the frontend and backend setup automatically.

1.  Clone this repository.
2.  Ensure Docker and Docker Compose are installed and running.
3.  From the project's root directory, run the command:
    ```bash
    docker-compose up --build
    ```
4.  Access the application in your web browser at `http://localhost:3000`.

---
## Final Project Structure
```
.
── .github/
│   └── workflows/
│       └── main.yml
├── backend/
│   └── ...
├── frontend/
│   └── ...
├── k8s/
│   ├── backend-deployment.yaml
│   ├── backend-service.yaml
│   ├── frontend-deployment.yaml
│   ├── frontend-service.yaml
│   └── ingress.yaml
├── docker-compose.yml
├── docker-compose.prod.yml
└── README.md
```
