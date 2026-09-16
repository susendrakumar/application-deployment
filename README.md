# Trendify Application Deployment – DevOps CI/CD Project

## Project Overview

This project demonstrates the deployment of a production-ready static web application using Docker, AWS EKS, Kubernetes, Jenkins, GitHub, Docker Hub, and Terraform.

The application is provided as pre-built production files inside the `dist/` directory. Nginx is used to serve the static application from a Docker container.

A Jenkins CI/CD pipeline is configured to automatically build a new Docker image, push it to Docker Hub, and deploy the updated image to the AWS EKS cluster whenever changes are pushed to the GitHub repository.

---

## Architecture

```text
Developer
   |
   | git push
   v
GitHub Repository
   |
   | GitHub Webhook
   v
Jenkins Pipeline
   |
   | Docker Build
   v
Docker Image
   |
   | Push
   v
Docker Hub
   |
   | Deploy
   v
AWS EKS
   |
   v
Kubernetes Deployment
   |
   v
LoadBalancer Service
   |
   v
Trendify Web Application
```

---

## Technologies Used

- Git & GitHub
- Jenkins
- Docker
- Docker Hub
- AWS EC2
- AWS EKS
- Kubernetes
- Terraform
- Nginx

---

## Repository Structure

```text
.
├── dist/
├── terraform/
│   ├── .terraform.lock.hcl
│   └── main.tf
├── .dockerignore
├── .gitignore
├── deployment.yaml
├── Dockerfile
├── Jenkinsfile
├── README.md
└── service.yaml
```

---

## Docker Deployment

The application uses Nginx to serve the pre-built files available in the `dist/` directory.

### Dockerfile

```dockerfile
FROM nginx:alpine

COPY dist/ /usr/share/nginx/html/

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

The Docker image is published to Docker Hub as:

```text
susesk/trend-app
```

---

## Kubernetes Deployment

The application is deployed to an AWS EKS cluster.

### EKS Cluster

```text
Cluster Name: trend-cluster
Region: ap-south-1
```

The Kubernetes deployment runs the Trendify Docker image.

```bash
kubectl get pods
```

The application is exposed using a Kubernetes `LoadBalancer` service.

```bash
kubectl get svc trend-service
```

The LoadBalancer provides public access to the deployed application.

---

## Jenkins CI/CD Pipeline

Jenkins is hosted on an AWS EC2 instance.

Pipeline name:

```text
trend-app-pipeline
```

The pipeline performs the following workflow:

1. Checkout the latest code from GitHub.
2. Build the Docker image.
3. Tag the image using the Jenkins build number.
4. Authenticate with Docker Hub using Jenkins credentials.
5. Push the versioned Docker image to Docker Hub.
6. Push the `latest` Docker image to Docker Hub.
7. Update the Kubernetes deployment in AWS EKS.
8. Wait for the Kubernetes rollout to complete successfully.

---

## GitHub Webhook Automation

A GitHub webhook is configured with Jenkins.

Whenever a new commit is pushed to the `main` branch:

```text
GitHub Push
      ↓
GitHub Webhook
      ↓
Jenkins Pipeline Triggered
      ↓
Docker Image Build
      ↓
Docker Hub Push
      ↓
AWS EKS Deployment
```

This provides an automated CI/CD workflow without manually starting the Jenkins build.

---

## Terraform Infrastructure

Terraform is used to provision the infrastructure required for the Jenkins server.

The Terraform configuration is available inside:

```text
terraform/main.tf
```

Terraform state files and the `.terraform` directory are excluded from Git using `.gitignore`.

Typical Terraform workflow:

```bash
terraform init
terraform plan
terraform apply
```

---

## Verification

### Verify Kubernetes Node

```bash
kubectl get nodes
```

### Verify Application Pod

```bash
kubectl get pods
```

Expected status:

```text
READY   STATUS
1/1     Running
```

### Verify LoadBalancer

```bash
kubectl get svc trend-service
```

### Verify Jenkins

Successful GitHub pushes automatically trigger the Jenkins pipeline. Successful pipeline executions build and publish the Docker image and roll out the updated application to EKS.

---

## CI/CD Workflow

```text
Code Change
   ↓
Git Push
   ↓
GitHub
   ↓
Webhook
   ↓
Jenkins
   ↓
Docker Build
   ↓
Docker Hub
   ↓
AWS EKS
   ↓
Kubernetes
   ↓
LoadBalancer
   ↓
Live Application
```

---

## Project Result

The Trendify application is successfully:

- Containerized using Docker and Nginx.
- Published through Docker Hub.
- Deployed to AWS EKS.
- Exposed publicly through a Kubernetes LoadBalancer.
- Integrated with Jenkins for CI/CD.
- Automatically triggered using a GitHub webhook.
- Supported by Terraform-based infrastructure configuration.

The complete deployment workflow from GitHub push to Kubernetes rollout has been tested successfully.