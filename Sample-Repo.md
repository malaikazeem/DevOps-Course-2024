# DevOps Integration for [nodejs-rest-api](https://github.com/neverovski/nodejs-rest-api)

## Overview
The [nodejs-rest-api](https://github.com/neverovski/nodejs-rest-api) repository contains a RESTful API built using Node.js. It provides basic CRUD operations and serves as a foundation for backend applications. This document integrates essential DevOps tools such as Docker, Kubernetes, Jenkins, and GitHub Actions to automate and streamline the development and deployment process.

---

## Applying DevOps Tools

### 1. Docker
#### Purpose:
Containerize the Node.js REST API application to ensure consistency across environments.

#### Dockerfile:
```dockerfile
FROM node:16
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

#### Commands:
```bash
# Build Docker Image
docker build -t nodejs-rest-api .

# Run Docker Container
docker run -p 3000:3000 nodejs-rest-api
```

---

### 2. Kubernetes
#### Purpose:
Deploy and manage the REST API in a Kubernetes cluster for high availability and scalability.

#### Kubernetes Configuration (deployment.yaml):
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nodejs-rest-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nodejs-rest-api
  template:
    metadata:
      labels:
        app: nodejs-rest-api
    spec:
      containers:
      - name: nodejs-rest-api
        image: nodejs-rest-api:latest
        ports:
        - containerPort: 3000
---
apiVersion: v1
kind: Service
metadata:
  name: nodejs-service
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 3000
  selector:
    app: nodejs-rest-api
```

#### Commands:
```bash
# Apply Kubernetes Configuration
kubectl apply -f deployment.yaml
```

---

### 3. Jenkins
#### Purpose:
Automate the CI/CD pipeline for building, testing, and deploying the application.

#### Jenkinsfile:
```groovy
pipeline {
    agent any
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/neverovski/nodejs-rest-api.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t nodejs-rest-api .'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }
        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub-credentials', url: '']) {
                    sh 'docker tag nodejs-rest-api username/nodejs-rest-api:latest'
                    sh 'docker push username/nodejs-rest-api:latest'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
            }
        }
    }
}
```

---

### 4. GitHub Actions
#### Purpose:
Set up automated CI/CD workflows using GitHub Actions.

#### Workflow File (.github/workflows/main.yml):
```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout Repository
      uses: actions/checkout@v3

    - name: Set up Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '16'

    - name: Install Dependencies
      run: npm install

    - name: Run Tests
      run: npm test

    - name: Build Docker Image
      run: docker build -t username/nodejs-rest-api .

    - name: Push Docker Image to Docker Hub
      env:
        DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
        DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
      run: |
        echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin
        docker push username/nodejs-rest-api:latest

    - name: Deploy to Kubernetes
      run: kubectl apply -f deployment.yaml
```

---

## Conclusion
This integration leverages Docker for containerization, Kubernetes for orchestration, Jenkins for CI/CD pipeline automation, and GitHub Actions for repository-specific automation. These tools ensure a streamlined development and deployment process, providing reliability, scalability, and consistency for the Node.js REST API.

For more information, visit the repository: [nodejs-rest-api](https://github.com/neverovski/nodejs-rest-api). 

--- 
