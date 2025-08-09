# DevSecOps Pipeline – `local-dev-uc` Branch

This branch contains a Node.js full-stack project integrated with a Jenkins pipeline that includes code quality analysis, secrets scanning, and security scanning.

---

## 🧰 Tools Used

- **Jenkins** – CI/CD Automation
- **SonarQube** – Code Quality & Security
- **Gitleaks** – Secrets Detection
- **Trivy** – Filesystem Vulnerability Scanning
- **Node.js** – For JS syntax validation

---

## 🗂️ Jenkins Pipeline Overview

### Jenkinsfile

The pipeline consists of the following stages:

1. **Git Checkout** – Pulls code from `local-dev-uc` branch.
2. **Frontend Compilation** – Syntax check using Node.js for React code.
3. **Backend Compilation** – Syntax check for backend code.
4. **Gitleaks Scan** – Scans for hardcoded secrets.
5. **SonarQube Analysis** – Sends code to SonarQube server.
6. **Quality Gate Check** – Waits for SonarQube report status.
7. **Trivy FS Scan** – Scans local project directory for vulnerabilities.

---

## 🔧 Jenkins Server Setup (EC2)

### 1. Install Jenkins

```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins -y
sudo systemctl start jenkins
sudo systemctl enable jenkins
```
---

### 2. Open Jenkins in Browser
Go to: ```bash http://<JENKINS_SERVER_PUBLIC_IP>:8080 ```

### 3. Install Required Plugins
Install the following plugins:

- NodeJS
- Git
- SonarQube Scanner
- Trivy (custom install)
- Gitleaks (manual install via CLI)

## 🔍 SonarQube Server Setup (EC2)
### 1. Prerequisites
Install Java:
```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
```

### 2. Install SonarQube

#### Create a new user
```bash
sudo adduser sonar
sudo usermod -aG sudo sonar
su - sonar
```

#### Download and extract SonarQube
```bash
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.3.0.82913.zip
unzip sonarqube-10.3.0.82913.zip
cd sonarqube-10.3.0.82913/bin/linux-x86-64/
./sonar.sh start
Access SonarQube at: http://<SONARQUBE_SERVER_PUBLIC_IP>:9000

Default credentials: admin / admin
```
---
### 3. Create Token & Webhook
- Create a Token under My Account → Security.

- Add Webhook under Project Settings → Webhooks:

- http://<JENKINS_SERVER_IP>:8080/sonarqube-webhook/

### ⚙️ Install Sonar Scanner Tool in Jenkins
- Go to Manage Jenkins → Global Tool Configuration

- Under SonarQube Scanner, click Add SonarQube Scanner

- Name it: sonar-scanner

- Choose "Install automatically" (or specify path if installed manually)

## 🐳 Install Trivy on Jenkins Server

### Install Trivy
```bash
sudo apt install wget -y
wget https://github.com/aquasecurity/trivy/releases/latest/download/trivy_0.51.1_Linux-64bit.deb
sudo dpkg -i trivy_0.51.1_Linux-64bit.deb
```
---

### Test
```bash trivy --version ```

#### 🔐 Install Gitleaks on Jenkins Server
```bash 
wget https://github.com/gitleaks/gitleaks/releases/download/v8.18.2/gitleaks_8.18.2_linux_x64.tar.gz
tar -xvzf gitleaks_8.18.2_linux_x64.tar.gz
sudo mv gitleaks /usr/local/bin/
```

---

#### gitleaks version
🔑 Add Sonar Token to Jenkins Credentials
Go to ** Manage Jenkins → Credentials → Global **

Click Add Credentials

Type: Secret Text

ID: sonar-token

Secret: (Paste SonarQube token)

---

### 🧪 Run the Pipeline
Once setup is complete:

Trigger the Jenkins job manually or via webhook

Monitor stages: Git checkout → Compilation → Gitleaks → Sonar → Trivy

Check SonarQube dashboard for results

---

## 📬 Author
Created by: Uday Chopade
GitHub: @udaychopade27

---
## 🙏 Credits
This project is originally created by [Aditya Jaiswal](https://github.com/jaiswaladi246).
I have followed his [YouTube](https://youtube.com/playlist?list=PLAdTNzDIZj_8bL9tHTXma8Lk89ygTvi6D&si=6DYLMdvgTpxFenO5) Playlist for implementation, while adding my own configurations, setup, and hands-on deployment on my environment.

## 📌 My Implementation
While the base project belongs to Aditya Jaiswal, 
I have:
Implemented the pipeline end-to-end on my local & AWS setup.
Customized configurations for my environment.

