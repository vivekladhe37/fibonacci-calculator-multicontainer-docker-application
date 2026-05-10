# Multi-Container Fibonacci Calculator

A complex, production-ready full-stack application designed to demonstrate the orchestration of multiple Docker containers, a worker-process architecture, and a fully automated CI/CD pipeline deploying to AWS.

## 🏗 Architecture & Flow

The application utilizes a microservices-like architecture where different responsibilities are handled by specialized containers. 

![Application Flow](./flow.png)

### Components:
- **Nginx**: Acts as a reverse proxy, routing traffic to either the React frontend or the Express API.
- **React Client**: Production-grade frontend served via Nginx.
- **Express Server**: The API layer that handles requests, stores data in PostgreSQL, and pushes tasks to Redis.
- **Worker Process**: A standalone Node.js process that watches Redis for new indices and calculates Fibonacci values.
- **PostgreSQL**: Relational database for persistent storage of seen indices and their calculated values.
- **Redis**: Used as a temporary data store and a message broker (Pub/Sub) for the worker process.

## 🧠 Learning Journey & Context

This project was developed in distinct phases, moving from simple containerization to complex cloud orchestration.

![Learning Context](./context.png)

1.  **Phase 1: Local Development**: Mastering Docker commands and creating development-specific Dockerfiles.
2.  **Phase 2: Multi-Container Orchestration**: Using Docker Compose to manage the local interaction between five different services.
3.  **Phase 3: CI/CD Pipeline**: Integrating Travis CI to automate testing and image builds.
4.  **Phase 4: Production Cloud Infrastructure**: Deploying to AWS using Elastic Beanstalk (Web tier), RDS (PostgreSQL), and ElastiCache (Redis).

## 🛠 Tech Stack & Skills

- **Containers**: Docker, Docker Compose
- **Frontend**: React.js
- **Backend**: Node.js, Express
- **Databases**: PostgreSQL, Redis
- **Proxy/Web Server**: Nginx
- **CI/CD**: Travis CI
- **Cloud (AWS)**: 
    - **Elastic Beanstalk**: Orchestrating the multi-container environment.
    - **RDS**: Managed PostgreSQL.
    - **ElastiCache**: Managed Redis cluster.
    - **S3**: Deployment artifact storage.
    - **IAM**: Secure access management.
    - **VPC Security Groups**: Networking security for database and cache access.

## 🚀 CI/CD Pipeline

The project features a robust automated pipeline defined in `.travis.yml`:
1.  **Test Phase**: Builds a development image and runs the test suite.
2.  **Build Phase**: Upon a successful test, builds production-ready images for the client, server, worker, and Nginx.
3.  **Push Phase**: Pushes the versioned images to Docker Hub.
4.  **Deploy Phase**: Triggers a deployment to AWS Elastic Beanstalk, which pulls the latest images and updates the environment.

## 🔧 Local Setup

To run the application locally in development mode:

```bash
docker-compose up --build
```

The application will be available at `http://localhost:3050`.

## ☁️ Production Deployment

Detailed steps for reproducing the AWS production environment can be found in the AWS Configuration Cheat Sheet. Key steps include:

1.  Provisioning an **Elastic Beanstalk** multi-docker environment.
2.  Setting up an **RDS PostgreSQL** instance (Free Tier).
3.  Configuring **ElastiCache Redis** with Transit Encryption (Preferred).
4.  Creating a **Custom Security Group** to allow internal traffic on ports `5432` and `6379`.
5.  Configuring Environment Variables in Elastic Beanstalk for database/redis connectivity.

---
*This project serves as a comprehensive showcase of my ability to design, containerize, and deploy scalable web applications using modern DevOps best practices.*