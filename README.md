# 🚀 DevOps CI/CD Pipeline — Node.js + Docker + Kubernetes + Jenkins + Ansible

A fully automated DevOps pipeline that containerises a Node.js application and deploys it to a Kubernetes cluster using Jenkins CI/CD, Docker, and Ansible — with zero-downtime rolling updates triggered automatically on every GitHub push.

---

## 📌 Project Overview

This project demonstrates a complete DevOps workflow:

1. **Docker** — Containerise the Node.js app and push to DockerHub
2. **Ansible** — Automate Kubernetes setup and deployment using playbooks
3. **Jenkins** — Automated CI/CD pipeline that builds, tests, pushes, and deploys on every code change
4. **Kubernetes** — Run the app with 3 replicas and a NodePort service with rolling updates

---

## 🏗️ Architecture

```
Developer pushes code
        ↓
    GitHub Repo
        ↓
  Jenkins (Poll SCM - every 1 min)
        ↓
  ┌─────────────────────────────────┐
  │         Jenkins Pipeline         │
  │  1. Checkout  → Pull from GitHub │
  │  2. Build     → Docker build     │
  │  3. Test      → Run container    │
  │  4. Push      → Push to DockerHub│
  │  5. Deploy    → kubectl rollout  │
  └─────────────────────────────────┘
        ↓
  Kubernetes Cluster (Minikube)
  3 Pods running the updated app
        ↓
  App accessible via NodePort
```

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| Node.js | Application runtime |
| Docker | Containerisation |
| DockerHub | Container image registry |
| Ansible | Infrastructure automation |
| Minikube | Local Kubernetes cluster |
| kubectl | Kubernetes CLI |
| Jenkins | CI/CD pipeline automation |
| GitHub | Source code & version control |
| Linux (Ubuntu) | Build & Production servers |

---

## 📁 Project Structure

```
devops-cw2/
├── server.js                        # Node.js application
├── Dockerfile                       # Docker image definition
├── Jenkinsfile                      # CI/CD pipeline definition
├── ansible-playbooks/
│   ├── install_kubectl.yml          # Playbook 1 — Install kubectl
│   ├── install_minikube.yml         # Playbook 2 — Install & start Minikube
│   ├── deploy_kubernetes.yml        # Playbook 3 — Deploy app to Kubernetes
│   └── service_scale.yml            # Playbook 4 — Scale & expose service
└── README.md
```

---

## 🐳 Dockerfile

```dockerfile
FROM node:alpine
WORKDIR /app
COPY server.js .
EXPOSE 8081
CMD ["node", "server.js"]
```

- Uses lightweight `node:alpine` base image
- Exposes port `8081`
- Runs `server.js` on container start

---

## ⚙️ Jenkins Pipeline Stages

| Stage | What it does |
|-------|-------------|
| ✅ Checkout | Pulls latest code from GitHub via SSH |
| ✅ Build | Builds Docker image with `latest` and build-number tags |
| ✅ Test | Runs test container, checks Node.js version, cleans up |
| ✅ Push | Pushes both image tags to DockerHub |
| ✅ Deploy | SSH into production server, runs `kubectl set image` for rolling update |

---

## 📋 Ansible Playbooks

### Playbook 1 — Install kubectl
```bash
ansible-playbook install_kubectl.yml
```
Installs and configures `kubectl` on the production server.

### Playbook 2 — Install Minikube
```bash
ansible-playbook install_minikube.yml
```
Installs Minikube and starts the local Kubernetes cluster.

### Playbook 3 — Deploy to Kubernetes
```bash
ansible-playbook deploy_kubernetes.yml
```
Creates the Kubernetes deployment with 3 replicas and exposes it as a NodePort service.

### Playbook 4 — Scale & Service
```bash
ansible-playbook service_scale.yml
```
Verifies scaling and confirms the NodePort service is running.

---

## 🚦 How to Run

### Prerequisites
- Ubuntu server (Build Server + Production Server)
- Docker installed on Build Server
- Jenkins installed on Build Server
- Ansible installed
- Minikube + kubectl installed on Production Server

### Step 1 — Clone the repo
```bash
git clone https://github.com/aliayubawan51/devops-cw2.git
cd devops-cw2
```

### Step 2 — Run Ansible playbooks (on Production Server)
```bash
cd ansible-playbooks
ansible-playbook install_kubectl.yml
ansible-playbook install_minikube.yml
ansible-playbook deploy_kubernetes.yml
ansible-playbook service_scale.yml
```

### Step 3 — Verify deployment
```bash
kubectl get pods
kubectl get services
curl http://$(minikube ip):<NodePort>
# Expected: DevOps Coursework 2! | v=0
```

### Step 4 — Trigger the pipeline
Push any change to GitHub:
```bash
git add .
git commit -m "Update app version"
git push
```
Jenkins automatically detects the push, runs the full pipeline, and deploys the update.

---

## 🔄 CI/CD Demo — v=0 to v=1

| Step | Action | Result |
|------|--------|--------|
| 1 | `curl http://<minikube-ip>:<port>` | Returns `v=0` |
| 2 | Edit `server.js` → change `v=0` to `v=1` | Code updated locally |
| 3 | `git push` to GitHub | Triggers Jenkins automatically |
| 4 | Jenkins runs all 5 stages | All green ✅ |
| 5 | `curl http://<minikube-ip>:<port>` | Returns `v=1` |

---

## 📦 DockerHub

Image available at:
```
docker pull akhan360/cw2-server:latest
```

---

## 👤 Author

**Muhammad Ali Ayub**
- GitHub: [@aliayubawan51](https://github.com/aliayubawan51)
- LinkedIn: [muhammad-a-765989297](https://linkedin.com/in/muhammad-a-765989297)
- Email: aliayubmuhammad1@gmail.com
