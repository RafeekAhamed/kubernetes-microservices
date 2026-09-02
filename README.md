# Production-Ready Microservices Deployment & CI/CD Using Kubernetes

## 📌 Project Overview

This project demonstrates the deployment and management of a containerized microservices application using **Kubernetes**.

The application consists of a **Frontend, Backend API, and PostgreSQL database**, deployed using Kubernetes workloads and services. The project also includes **Docker containerization, Helm-based deployment, health probes, resource management, RBAC, Ingress, Horizontal Pod Autoscaling, rolling updates, rollback, and GitHub Actions CI/CD**.

The project is designed to demonstrate practical Kubernetes and DevOps skills required for production-oriented container orchestration.

---

## 🏗️ Architecture

```text
                    ┌──────────────────────┐
                    │       GitHub         │
                    │   Source Repository  │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │   GitHub Actions     │
                    │     CI/CD Pipeline   │
                    └──────────┬───────────┘
                               │
                         Docker Build
                               │
                               ▼
                    ┌──────────────────────┐
                    │   Container Registry │
                    └──────────┬───────────┘
                               │
                               ▼
                 ┌────────────────────────────┐
                 │        Kubernetes          │
                 │      microservices NS       │
                 │                            │
                 │  ┌──────────────────────┐  │
                 │  │       Ingress        │  │
                 │  └──────────┬───────────┘  │
                 │             │              │
                 │      ┌──────┴──────┐       │
                 │      ▼             ▼       │
                 │  Frontend       Backend    │
                 │  Deployment     Deployment │
                 │                    │        │
                 │                    ▼        │
                 │              PostgreSQL     │
                 │              Deployment     │
                 └────────────────────────────┘
```

---

## 🛠️ Technologies

| Technology     | Purpose                          |
| -------------- | -------------------------------- |
| Kubernetes     | Container orchestration          |
| Docker         | Application containerization     |
| Helm           | Kubernetes package management    |
| GitHub Actions | CI/CD automation                 |
| PostgreSQL     | Database                         |
| Linux          | Container/Kubernetes environment |
| YAML           | Kubernetes configuration         |
| Git/GitHub     | Source control                   |
| kubectl        | Kubernetes administration        |

---

## 📂 Project Structure

```text
kubernetes-microservices/
│
├── backend/
│   ├── app.py
│   ├── requirements.txt
│   └── Dockerfile
│
├── frontend/
│   ├── Dockerfile
│   └── ...
│
├── k8s/
│   ├── namespace.yaml
│   ├── configmap.yaml
│   ├── secret.yaml
│   ├── postgres-deployment.yaml
│   ├── postgres-service.yaml
│   ├── backend-deployment.yaml
│   ├── backend-service.yaml
│   ├── frontend-deployment.yaml
│   ├── frontend-service.yaml
│   ├── rbac.yaml
│   ├── ingress.yaml
│   └── hpa.yaml
│
├── helm/
│   └── microservices/
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
│
├── .github/
│   └── workflows/
│       └── ci-cd.yml
│
├── .gitignore
└── README.md
```

---

# 🚀 Deployment Workflow

## 1. Clone the Repository

```bash
git clone https://github.com/RafeekAhamed/kubernetes-microservices.git
cd kubernetes-microservices
```

---

## 2. Build Docker Images

Build the backend image:

```bash
docker build -t backend:latest ./backend
```

Build the frontend image:

```bash
docker build -t frontend:latest ./frontend
```

Verify images:

```bash
docker images
```

---

## 3. Test Docker Containers

Run the backend:

```bash
docker run -d -p 5000:5000 --name backend backend:latest
```

Test the health endpoint:

```bash
curl http://localhost:5000/health
```

Expected response:

```json
{
  "status": "healthy"
}
```

---

# ☸️ Kubernetes Deployment

## 4. Create Namespace

```bash
kubectl apply -f k8s/namespace.yaml
```

Verify:

```bash
kubectl get namespaces
```

---

## 5. Deploy ConfigMap

```bash
kubectl apply -f k8s/configmap.yaml -n microservices
```

Verify:

```bash
kubectl get configmap -n microservices
```

---

## 6. Deploy Secret

```bash
kubectl apply -f k8s/secret.yaml -n microservices
```

Verify:

```bash
kubectl get secrets -n microservices
```

> Kubernetes Secrets should be handled securely in real production environments. Avoid committing real credentials to GitHub.

---

## 7. Deploy PostgreSQL

```bash
kubectl apply -f k8s/postgres-deployment.yaml -n microservices
kubectl apply -f k8s/postgres-service.yaml -n microservices
```

Verify:

```bash
kubectl get pods -n microservices
kubectl get svc -n microservices
```

---

## 8. Deploy Backend

```bash
kubectl apply -f k8s/backend-deployment.yaml -n microservices
kubectl apply -f k8s/backend-service.yaml -n microservices
```

Verify:

```bash
kubectl get deployment backend -n microservices
kubectl get pods -l app=backend -n microservices
```

---

## 9. Deploy Frontend

```bash
kubectl apply -f k8s/frontend-deployment.yaml -n microservices
kubectl apply -f k8s/frontend-service.yaml -n microservices
```

Verify:

```bash
kubectl get deployment frontend -n microservices
kubectl get pods -l app=frontend -n microservices
```

---

# 🔍 Health Checks

The backend deployment uses Kubernetes:

* Readiness Probe
* Liveness Probe

Example:

```yaml
readinessProbe:
  httpGet:
    path: /health
    port: 5000
  initialDelaySeconds: 5
  periodSeconds: 10

livenessProbe:
  httpGet:
    path: /health
    port: 5000
  initialDelaySeconds: 15
  periodSeconds: 20
```

These probes allow Kubernetes to determine whether the application is ready to receive traffic and whether the container is healthy.

---

# 📊 Resource Management

CPU and memory requests/limits are configured for workloads.

Example:

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"
```

This helps Kubernetes schedule workloads efficiently and prevent uncontrolled resource consumption.

---

# 🔐 RBAC

Kubernetes **Role-Based Access Control (RBAC)** is configured to control access to cluster resources.

Components include:

* ServiceAccount
* Role
* RoleBinding

Apply RBAC:

```bash
kubectl apply -f k8s/rbac.yaml -n microservices
```

Verify:

```bash
kubectl get role -n microservices
kubectl get rolebinding -n microservices
kubectl get serviceaccount -n microservices
```

---

# 🌐 Ingress

Ingress provides HTTP/HTTPS routing to Kubernetes services.

Apply:

```bash
kubectl apply -f k8s/ingress.yaml -n microservices
```

Verify:

```bash
kubectl get ingress -n microservices
```

---

# 📈 Horizontal Pod Autoscaling

HPA is configured to automatically scale workloads based on resource utilization.

Apply:

```bash
kubectl apply -f k8s/hpa.yaml -n microservices
```

Check:

```bash
kubectl get hpa -n microservices
```

Monitor:

```bash
kubectl get hpa -n microservices -w
```

---

# 📦 Helm Deployment

The project uses Helm to package and deploy Kubernetes resources.

Validate the chart:

```bash
helm lint helm/microservices
```

Render templates:

```bash
helm template microservices helm/microservices
```

Install:

```bash
helm install microservices helm/microservices \
  --namespace microservices \
  --create-namespace
```

Check the release:

```bash
helm list -n microservices
```

Upgrade:

```bash
helm upgrade microservices helm/microservices \
  --namespace microservices
```

Rollback:

```bash
helm rollback microservices 1 \
  --namespace microservices
```

---

# 🔄 Rolling Updates

Kubernetes Deployments support rolling updates without stopping the entire application.

Example:

```bash
kubectl set image deployment/backend \
  backend=backend:v2 \
  -n microservices
```

Monitor:

```bash
kubectl rollout status deployment/backend -n microservices
```

View rollout history:

```bash
kubectl rollout history deployment/backend -n microservices
```

---

# ↩️ Rollback

If a deployment introduces an issue:

```bash
kubectl rollout undo deployment/backend -n microservices
```

Verify:

```bash
kubectl rollout status deployment/backend -n microservices
```

---

# 🔁 CI/CD with GitHub Actions

The project includes a GitHub Actions workflow that automates:

```text
Developer
    │
    ▼
Git Push
    │
    ▼
GitHub Actions
    │
    ├── Build Docker Image
    │
    ├── Run Validation
    │
    ├── Push Image
    │
    └── Deploy to Kubernetes
```

Typical pipeline stages:

1. Checkout source code
2. Build Docker image
3. Run application validation
4. Authenticate with container registry
5. Push Docker image
6. Deploy/update Kubernetes workloads
7. Verify deployment

---

# 🧪 Application Testing

Check all Kubernetes resources:

```bash
kubectl get all -n microservices
```

Check pods:

```bash
kubectl get pods -n microservices -o wide
```

Check services:

```bash
kubectl get svc -n microservices
```

Check deployments:

```bash
kubectl get deployments -n microservices
```

Check endpoints:

```bash
kubectl get endpoints -n microservices
```

---

# 🔧 Troubleshooting

View pod logs:

```bash
kubectl logs <pod-name> -n microservices
```

Follow logs:

```bash
kubectl logs -f <pod-name> -n microservices
```

Describe a pod:

```bash
kubectl describe pod <pod-name> -n microservices
```

Describe deployment:

```bash
kubectl describe deployment backend -n microservices
```

Check events:

```bash
kubectl get events -n microservices --sort-by=.lastTimestamp
```

Check service:

```bash
kubectl describe svc backend -n microservices
```

---

# 📌 Key Kubernetes Concepts Demonstrated

* Kubernetes Namespaces
* Deployments
* ReplicaSets
* Pods
* Services
* ConfigMaps
* Secrets
* RBAC
* ServiceAccounts
* Ingress
* Resource Requests & Limits
* Readiness Probes
* Liveness Probes
* Horizontal Pod Autoscaler
* Rolling Updates
* Rollbacks
* Helm
* Kubernetes Troubleshooting
* Container Networking
* CI/CD Automation

---

# 🎯 Project Objectives

The primary objectives of this project are to demonstrate practical experience with:

* Containerized application deployment
* Kubernetes workload management
* Microservices architecture
* Infrastructure configuration using YAML
* Helm-based application packaging
* Kubernetes security using RBAC
* Application health monitoring
* Horizontal scaling
* Zero/minimal-downtime rolling deployments
* Deployment rollback
* CI/CD automation using GitHub Actions

---

# 💼 Resume Highlights

**Production-Ready Microservices Deployment & CI/CD Using Kubernetes**

* Containerized microservices using Docker and deployed applications on Kubernetes using Deployments, Services, ConfigMaps, and Secrets.
* Implemented Kubernetes readiness/liveness probes, resource requests/limits, RBAC, Ingress, and Horizontal Pod Autoscaling.
* Packaged Kubernetes workloads using Helm and implemented rolling updates, deployment rollback, and operational troubleshooting.
* Built GitHub Actions CI/CD automation for Docker image build, registry push, and Kubernetes deployment.

---

# 👨‍💻 Author

**Rafeek Ahamed M**

DevOps Engineer | Azure Cloud Engineer

GitHub: `https://github.com/RafeekAhamed`

LinkedIn: `https://linkedin.com/in/rafeek-ahamed-devops`

---

## ⭐ Project Status

**Status:** In Progress / Production-Ready Kubernetes Lab

This project is continuously enhanced with additional Kubernetes, DevOps, CI/CD, security, observability, and automation capabilities.
