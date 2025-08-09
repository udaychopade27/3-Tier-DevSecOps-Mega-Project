#  DevSecOps Mega Project – `dev-k8s-uc` Branch

This branch extends the previous pipeline by deploying the 3-Tier Notes App (React frontend, Node.js backend, MySQL) on **Amazon EKS**, using **Terraform**, **Kubernetes manifests**, and secure access control (RBAC). This is the culmination of your DevSecOps journey—from code to AWS-hosted container orchestration.

---

##  Project Structure

```text
.
├── api/                      # Backend source with Dockerfile
├── client/                   # Frontend (React) with Dockerfile + Nginx config
├── k8s-dev/                  # Kubernetes manifests for deployment
│   ├── backend.yaml
│   ├── frontend.yaml
│   ├── database.yaml
│   ├── sc.yaml              # StorageClass definition
│── terrafromrbac/            # Terraform scripts for provisioning EKS cluster and RBAC setup
│   ├── main.tf
│   ├── variables.tf
│   ├── output.tf
│   ├── rbac/                # RBAC manifests (ClusterRole, Role, Bindings, ServiceAccount, etc.)
├── Jenkinsfile               # CI/CD pipeline (includes deploy-to-K8s stage)
├── README.md                 # This documentation
```
---

## Architecture Overview
   You're leveraging:

- **Terraform** for provisioning AWS VPC, subnets, internet gateway, EKS cluster, and Node Groups.

- **AWS EKS** Addon for AWS EBS CSI driver—for dynamic persistent storage.

- **Kubernetes** manifests using ConfigMaps, Secrets, persistent volumes, Deployments, and LoadBalancer Services.

- **RBAC configuration:** ClusterRole, Role, RoleBinding, ClusterRoleBinding, and ServiceAccount for Jenkins’ integration.

- **CI/CD pipeline** in Jenkinsfile to build Docker images, run security scans (SonarQube, Gitleaks, Trivy), push to registry, and deploy to EKS.

---

## Setup Instructions
### 1. Provision AWS Infrastructure with Terraform
```bash
cd terrafromrbac
terraform init
terraform apply -auto-approve
```
This sets up the EKS cluster along with node group, networking, and outputs for cluster access.

### 2. Configure Kubernetes Cluster Access
Once EKS is ready:

```bash
aws eks update-kubeconfig --region <your-region> --name devopsshack-cluster
```
Deploy EBS CSI driver via Terraform (already configured) and ensure storage is available for your database.

### 3. Apply RBAC Configurations for Jenkins
```bash
kubectl apply -f terrafromrbac/rbac/clusterole.yaml
kubectl apply -f terrafromrbac/rbac/role.yaml
kubectl apply -f terrafromrbac/rbac/rolebinding.yaml
kubectl apply -f terrafromrbac/rbac/clusterrolebinding.yaml
kubectl apply -f terrafromrbac/rbac/service.yaml
kubectl apply -f terrafromrbac/rbac/secrets.yaml
```
These grant Jenkins appropriate permissions to deploy and manage resources in the dev namespace.

### 4. Deploy Application to Kubernetes
```bash
kubectl apply -f k8s-dev/sc.yaml
kubectl apply -f k8s-dev/database.yaml
kubectl apply -f k8s-dev/backend.yaml
kubectl apply -f k8s-dev/frontend.yaml
```
This deploys MySQL with persistent storage, the backend (Node.js), and the frontend (React with NGINX).

### 5. CI/CD Pipeline – Jenkins Integration
   1. Your Jenkinsfile now includes the Kubernetes deployment stage:
   2. Build and tag Docker images.
   3. Push to container registry.
   4. Use kubectl to apply Kubernetes manifests.

   Optional: Clean up and rollout updates automatically.

---

## DevSecOps Tools Summary
**SonarQube:** Automated static code analysis inside Jenkins.
**Gitleaks:** Secret scanning for source code.
**Trivy:** Vulnerability scanning for filesystem and image layers.
**RBAC:** Secures Jenkins access to Kubernetes resources using least-privilege.

---

## Accessing Your Application
After successful deployment:

**Backend:** Exposed via LoadBalancer service in AWS.
**Frontend:** Accessible via its own LoadBalancer—grab the external IP/hostname.

---

## Next Steps
Implement Ingress with custom domain routing.
Add Monitoring & Logging: Prometheus, Grafana, Elasticsearch, etc.
Add automated scaling, SSL/TLS, and cost optimization in EKS.

---
## 🙏 Credits
This project is originally created by [Aditya Jaiswal](https://github.com/jaiswaladi246).
I have followed his [YouTube](https://youtube.com/playlist?list=PLAdTNzDIZj_8bL9tHTXma8Lk89ygTvi6D&si=6DYLMdvgTpxFenO5) Playlist for implementation, while adding my own configurations, setup, and hands-on deployment on my environment.

## 📌 My Implementation
While the base project belongs to Aditya Jaiswal, 
I have:
Implemented the pipeline end-to-end on my local & AWS setup.
Customized configurations for my environment.