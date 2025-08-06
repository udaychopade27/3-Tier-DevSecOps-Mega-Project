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
Project Key: NodeJS-Project

Credentials ID in Jenkins: sonar-token

Webhook in SonarQube:
http://<jenkins-server>:8080/sonarqube-webhook/

### ✅ DockerHub
Docker images used:
   uday27/frontend:latest
   uday27/backend:latest

Jenkins Credentials ID: docker-cred

### ✅ Trivy & Gitleaks Usage
Trivy:
```bash
trivy fs --format table -o fs-report.html .
trivy image --format table -o backend-image-report.html uday27/backend:latest
trivy image --format table -o frontend-image-report.html uday27/frontend:latest
```
Gitleaks:
```bash
gitleaks detect --source ./client --exit-code 1
gitleaks detect --source ./api --exit-code 1
```

### ✅ Run the Jenkins Pipeline
Push changes to docker-build-uc branch

Jenkins Pipeline will:

Test & scan code

Build Docker images

Push to DockerHub

Deploy app using Docker Compose

## 🌐 App Access (After Deployment)
Frontend: http://jenkins-server-ip:3000

Backend: http://jenkins-server-ip:5000/api

## 👤 Maintainer
Created by: Uday Chopade
🔗 [GitHub](https://www.github.com/udaychopade27)
