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

