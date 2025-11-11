# 🏋️‍♂️ ACEest Fitness - Flask Web Application

A modular, containerized **Flask-based Fitness and Gym Management System** built as part of a DevOps assignment, demonstrating complete CI/CD integration using **Jenkins**, **Docker**, **Kubernetes**, and **SonarQube**.

---

## 📦 Repository Links

- **GitHub Repository:** [ACE_est-Fitness](https://github.com/2024ht66047/ACE_est-Fitness.git)  
- **Docker Hub Repository:** [2024ht66047/my-image](https://hub.docker.com/r/2024ht66047/my-image)  
  (Tagged per version, e.g., `2024ht66047/my-image:v1`, `v2`, etc.)

---

## 📋 Project Overview

This project represents the full DevOps pipeline of a **Fitness and Gym Management System**, including:
- Flask web application development  
- Version control with Git/GitHub  
- Automated builds and tests with Jenkins  
- Containerization with Docker  
- Deployment via Kubernetes  
- Static code analysis using SonarQube  

---

## 🚀 Features & Objectives

### 1. Application Development
- Core application developed using **Flask** (Python).
- Based on the provided file: `ACEest_Fitness.py` and its improved versions.
- Modular, maintainable, and Pythonic codebase.

---

### 2. Version Control Setup
- Local Git initialized and linked with [GitHub Repository](https://github.com/2024ht66047/ACE_est-Fitness.git).
- Structured commits and branches for:
  - Feature additions  
  - Bug fixes  
  - Infrastructure updates  
  - Version tagging (e.g., `v1.0`, `v2.0`, etc.)

---

### 3. Unit Testing and Test Automation
- **Pytest** used for testing Flask routes, endpoints, and functions.
- Automated test execution integrated with Jenkins pipeline.
- Ensures every commit passes tests before deployment.

---

### 4. Continuous Integration (CI) with Jenkins
- **Jenkins** hosted on `localhost` acts as the CI server.
- Configured to poll GitHub for changes or use webhooks.
- Stages include:
  - Code checkout  
  - Unit testing with Pytest  
  - SonarQube static analysis  
  - Docker image build and push  

---

### 5. Containerization with Docker and Podman
- Flask application containerized using a **Dockerfile**.
- Docker images are version-controlled and pushed to:
  - **Docker Hub:** `2024ht66047/my-image:<tag>`
- Supports multiple tags (`v1`, `v2`, etc.) for different builds.

**Example commands:**
```bash
docker build -t 2024ht66047/my-image:v1 .
docker push 2024ht66047/my-image:v1
```
. Continuous Delivery & Deployment (CD)

Application deployed on Kubernetes using Minikube or compatible cluster.

Deployment manifest: flask-app-deployment.yaml

Deployment strategies implemented:

🔵 Blue-Green Deployment

🟡 Canary Release

⚫ Shadow Deployment

🧪 A/B Testing

🔁 Rolling Update

Rollback mechanisms ensure revert to last stable version on failure.
```
Kubernetes Deployment Example:
kubectl apply -f flask-app-deployment.yaml
kubectl get pods
kubectl get svc
```
### Automated Build, Test & Quality Analysis Integration

Jenkins pipeline automates build → test → analysis → deploy.

Pytest executed within Dockerized environment.

SonarQube (localhost) used for:

Static code analysis

Code coverage

Quality gate enforcement

### Key Learning Outcomes

Build and modularize a Flask web app using best practices.

Manage and track code versions with Git and GitHub.

Set up a complete CI/CD pipeline with Jenkins.

Automate unit tests and code quality checks.

Containerize and deploy applications using Docker and Kubernetes.

Implement advanced deployment and rollback strategies.

####Setup & Installation
Clone the repository
```
git clone https://github.com/2024ht66047/ACE_est-Fitness.git
cd ACE_est-Fitness
```
####Create virtual environment and install dependencies
```
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
flask run
```
####Docker Workflow
```
docker build -t 2024ht66047/my-image:v1 .
docker run -p 5000:5000 2024ht66047/my-image:v1
docker push 2024ht66047/my-image:v1
```
####Kubernetes Deployment
```
kubectl apply -f flask-app-deployment.yaml
kubectl get pods
kubectl get svc
```
####Pipelineflow :
```
GitHub Commit → Jenkins Build → Pytest → SonarQube → Docker Build → Docker Hub Push → K8s Deploy
```
