# DevOps Implementation for Node.js REST API

This document describes the application of DevOps tools to enhance the development, deployment, and monitoring of a **Node.js REST API** project. The selected repository showcases a CRUD API built with Express.js and MongoDB.

---

## Repository Overview

**Original Repository:** [NodeJS-RestAPI](https://github.com/TaniaLDavidson/NodeJS-RestAPI)  
**Features:**
- RESTful APIs using Express.js
- MongoDB for database management
- CRUD operations for managing tasks

---

## Applying DevOps Tools

### 1. **Containerization with Docker**

#### Dockerfile:
```dockerfile
FROM node:16
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]
```

#### **Commands:**
- Build the Docker image:
`docker build -t nodejs-restapi`
- Run the container:
`docker run -p 3000:3000 nodejs-restapi`

---

### 2. **Orchestration with Kubernetes**
A Kubernetes configuration file (k8s/deployment.yaml) is added to deploy the application with replicas and expose it as a service.

#### Deployment Configuration:
```yaml
Copy code
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nodejs-restapi
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nodejs-restapi
  template:
    metadata:
      labels:
        app: nodejs-restapi
    spec:
      containers:
      - name: nodejs-restapi
        image: nodejs-restapi:latest
        ports:
        - containerPort: 3000
---
apiVersion: v1
kind: Service
metadata:
  name: nodejs-restapi-service
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 3000
  selector:
    app: nodejs-restapi
```

#### **Commands:**
- Deploy the application:
`kubectl apply -f k8s/deployment.yaml`

---

#### 3. **Continuous Integration with GitHub Actions**
A GitHub Actions workflow automates testing and deployment.

Workflow Configuration (.github/workflows/main.yml):
```yaml

name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    - name: Set up Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '16'
    - name: Install dependencies
      run: npm install
    - name: Run tests
      run: npm test
    - name: Build Docker image
      run: docker build -t username/nodejs-restapi .
    - name: Push Docker image
      env:
        DOCKER_HUB_USERNAME: ${{ secrets.DOCKER_HUB_USERNAME }}
        DOCKER_HUB_ACCESS_TOKEN: ${{ secrets.DOCKER_HUB_ACCESS_TOKEN }}
      run: |
        echo "${{ secrets.DOCKER_HUB_ACCESS_TOKEN }}" | docker login -u "${{ secrets.DOCKER_HUB_USERNAME }}" --password-stdin
        docker push username/nodejs-restapi:latest
  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
    - name: Set up Kubernetes CLI
      uses: azure/setup-kubectl@v3
    - name: Deploy to Kubernetes
      run: kubectl apply -f k8s/deployment.yaml
```

---


#### 4. **Monitoring with Prometheus and Grafana**
Steps:
- Add a /metrics endpoint in the Node.js API for Prometheus scraping.
- Deploy Prometheus and Grafana using Helm in the Kubernetes cluster.
- Configure Grafana to visualize Prometheus metrics in custom dashboards.

  
### Summary
By integrating Docker, Kubernetes, GitHub Actions, and monitoring solutions, this project benefits from:
- Automated testing, building, and deployment.
- Scalable, containerized infrastructure.
- Robust monitoring for performance and reliability.

