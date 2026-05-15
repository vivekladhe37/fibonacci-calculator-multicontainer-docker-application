# 🚀 Multi‑Container Fibonacci Calculator (Docker)

A production‑style, multi‑container application demonstrating Docker orchestration, worker‑based architecture, and a complete CI/CD pipeline deploying to AWS.  
This project showcases how to design, containerize, and deploy a scalable full‑stack system using modern DevOps practices.

---

## 🏗️ Architecture Overview

The system follows a microservices‑inspired pattern where each responsibility is isolated into its own container.

### Services

- **Nginx** — Reverse proxy routing traffic to the React client or Express API  
- **React Client** — Production‑ready frontend served via Nginx  
- **Express Server** — API layer that stores indices in PostgreSQL and publishes tasks to Redis  
- **Worker** — Background Node.js process that listens to Redis and computes Fibonacci values  
- **PostgreSQL** — Persistent database storing all submitted indices  
- **Redis** — In‑memory store + Pub/Sub broker for worker communication  

---

## 🔄 Data Flow

1. User submits an index from the React UI  
2. Express API stores the index in PostgreSQL  
3. Express publishes the index to Redis  
4. Worker receives the index, computes Fibonacci, and stores the result  
5. Client polls the API to display calculated values  

---

## 🧠 Learning Journey

This project was built in four phases:

### Phase 1 — Local Development
- Docker fundamentals  
- Dev‑specific Dockerfiles  
- Hot‑reload setup  

### Phase 2 — Multi‑Container Orchestration
- Docker Compose  
- Networking between 5 services  
- Environment variables & shared volumes  

### Phase 3 — CI/CD Pipeline
- Travis CI  
- Automated testing  
- Automated Docker image builds  
- Versioned image pushes to Docker Hub  

### Phase 4 — Production Deployment (AWS)
- Elastic Beanstalk (Multi‑Docker)  
- RDS PostgreSQL  
- ElastiCache Redis  
- S3 for deployment artifacts  
- IAM roles & security groups  
- Environment configuration  

---

## 🛠️ Tech Stack

**Frontend**
- React.js  
- Nginx  

**Backend**
- Node.js  
- Express  

**Data Layer**
- PostgreSQL (RDS)  
- Redis (ElastiCache)  

**DevOps & Cloud**
- Docker  
- Docker Compose  
- Travis CI  
- AWS Elastic Beanstalk  
- AWS RDS  
- AWS ElastiCache  
- AWS S3  
- IAM, VPC, Security Groups  

---

## 🚀 CI/CD Pipeline (Travis CI)

The `.travis.yml` pipeline performs:

1. **Test Phase** — Builds dev image and runs test suite  
2. **Build Phase** — Builds production images for Client, Server, Worker, and Nginx  
3. **Push Phase** — Pushes versioned images to Docker Hub  
4. **Deploy Phase** — Triggers Elastic Beanstalk deployment; Elastic Beanstalk pulls latest images and updates the environment  

---

## 🧪 Local Development

Start all services locally:

```bash
docker-compose up --build
```

App will be available at:

👉 **`http://localhost:3050`**

---

## ☁️ Production Deployment (AWS)

To reproduce the production environment:

### 1. Provision AWS Resources
- Elastic Beanstalk (Multi‑Docker)  
- RDS PostgreSQL  
- ElastiCache Redis  
- S3 bucket for Elastic Beanstalk artifacts  

### 2. Configure Networking
- Custom VPC security groups  
- Allow internal traffic on:
  - `5432` (PostgreSQL)  
  - `6379` (Redis)  

### 3. Set Environment Variables in Elastic Beanstalk
- `PGHOST`  
- `PGUSER`  
- `PGPASSWORD`  
- `PGDATABASE`  
- `REDIS_HOST`  
- `REDIS_PORT`  

### 4. Deploy via Travis CI
- Travis builds images  
- Pushes to Docker Hub  
- Elastic Beanstalk pulls latest images and updates the environment  

---

## 📁 Folder Structure

```text
/
├── client/             # React frontend
├── server/             # Express API
├── worker/             # Fibonacci worker
├── nginx/              # Reverse proxy config
├── docker-compose.yml
├── .travis.yml
├── AWS_CHEAT_SHEET.md
└── README.md
```


---

## 📘 Additional Documentation

- See [AWS_CHEAT_SHEET.md](./AWS_CHEAT_SHEET.md) for step‑by‑step AWS configuration.

---

## 🎯 Summary

This project demonstrates:

- Multi‑container architecture  
- Worker‑based background processing  
- Docker Compose orchestration  
- CI/CD automation  
- AWS production deployment  
- Real‑world DevOps patterns  

It serves as a strong portfolio piece showing the ability to design, containerize, and deploy scalable cloud‑native applications.
