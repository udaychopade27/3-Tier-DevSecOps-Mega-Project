# 🚀 3-Tier DevSecOps Project – `docker-build-uc` Branch

This branch demonstrates a complete DevSecOps CI/CD pipeline for a full-stack Node.js + React application using Jenkins, Docker, and Docker Compose. It integrates static code analysis, secret scanning, vulnerability scanning, and automated deployment.

---

## 🧰 Tools & Technologies

- **CI/CD**: Jenkins (Scripted Pipeline)
- **Code Quality**: SonarQube
- **Secrets Scan**: Gitleaks
- **Security Scan**: Trivy
- **Frontend**: React (served via Nginx)
- **Backend**: Node.js + Express
- **Database**: MySQL 8
- **Containerization**: Docker & Docker Compose

---

## 🗂️ Project Structure
```text .
├── api/ # Backend source code + Dockerfile.api
│ └── Dockerfile # Backend Dockerfile
├── client/ # Frontend source code + Dockerfile.client + Nginx config
│ ├── Dockerfile # Frontend Dockerfile
│ └── default.conf # Nginx config for React SPA
├── docker-compose.yml # Docker setup for backend, frontend, mysql
├── Jenkinsfile # CI/CD pipeline (this branch)
```
---

---

## ⚙️ Jenkins Pipeline Overview

### 📋 Jenkinsfile Summary

The Jenkins pipeline performs:

1. ✅ Git checkout from `docker-build-uc`
2. 🧪 Node.js syntax validation for frontend/backend
3. 🔐 Secret scanning using Gitleaks
4. 📊 Code quality check with SonarQube
5. ✅ Quality gate validation
6. 🛡️ Trivy filesystem scanning
7. 🐳 Docker build/tag/push backend image
8. 🐳 Docker build/tag/push frontend image
9. 🧩 Deployment via Docker Compose

---

## 🔧 Setup Instructions

### 1️⃣ Clone the Repository

```bash
git clone -b docker-build-uc https://github.com/udaychopade27/3-Tier-DevSecOps-Mega-Project.git
cd 3-Tier-DevSecOps-Mega-Project
```
---

## 🔐 DevSecOps Tools Setup
### ✅ SonarQube
Hosted on a separate EC2 instance (see previous branch setup).

Project Key: NodeJS-Project

Jenkins Credentials ID: sonar-token

Webhook: http://<jenkins-server>:8080/sonarqube-webhook/

### ✅ DockerHub
Docker images tagged and pushed:

uday27/frontend:latest

uday27/backend:latest

Jenkins Credentials ID: docker-cred

## ✅ Trivy & Gitleaks
### Trivy:
``bash
trivy fs --format table -o fs-report.html .
trivy image --format table -o backend-image-report.html uday27/backend:latest
trivy image --format table -o frontend-image-report.html uday27/frontend:latest
```
### Gitleaks:
``bash
gitleaks detect --source ./client --exit-code 1
gitleaks detect --source ./api --exit-code 1
```

## ✅ Running Jenkins Pipeline
Once everything is configured:

Commit & push changes to the docker-build-uc branch.

Jenkins will:

Build and test the code

Push Docker images

Deploy the full-stack app via Docker Compose

App will be accessible at:

**Frontend:** ```bash http://<jenkins-server-ip>:3000 ```

**Backend:** ```bash http://<jenkins-server-ip>:5000/api ```

## 📬 Maintainer
Created by: Uday Chopade
🔗 [GitHub](https://www.github.com/udaychopade27)