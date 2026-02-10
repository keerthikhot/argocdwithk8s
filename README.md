# Argo CD with Kubernetes (kind) – GitOps Demo Project

This repository demonstrates a **GitOps-based application delivery workflow** using **Argo CD** deployed on a **local Kubernetes cluster created with kind**.

The project shows how Kubernetes manifests stored in Git are continuously synchronized to a cluster using Argo CD.

---

## 🚀 Project Overview

This project covers:

- Creating a local Kubernetes cluster using **kind**
- Installing and configuring **Argo CD**
- Deploying applications declaratively using **Argo CD Application**
- Understanding GitOps principles (Git as the source of truth)
- Automated sync, self-healing, and pruning

---

## 🧰 Tools & Technologies Used

- **Kubernetes**
- **kind** (Kubernetes in Docker)
- **Argo CD**
- **Docker**
- **kubectl**
- **Git & GitHub**

---

## 📁 Repository Structure

argocdwithk8s/
├── k8s-kind-voting-app/
│ └── k8s-specifications/
│ ├── deployment.yaml
│ ├── service.yaml
│ └── other-k8s-manifests.yaml
├── README.md

---

## 🔧 Prerequisites

Make sure the following tools are installed:

- Docker
- kind
- kubectl
- Argo CD CLI (optional but recommended)

---

## 🧪 Step-by-Step Setup

### 1️⃣ Create Kubernetes Cluster using kind

```bash
kind create cluster --name argocd-cluster
```
Verify cluster:
```kubectl get nodes```
2️⃣ Install Argo CD
```kubectl create namespace argocd
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
Wait for pods:
kubectl get pods -n argocd
```
3️⃣ Access Argo CD UI
```Port-forward Argo CD server:
kubectl port-forward svc/argocd-server -n argocd 8080:443
Open in browser:
https://localhost:8080
```
4️⃣ Login to Argo CD
Get admin password:
```kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d
```
Login via CLI:
```argocd login localhost:8080 --username admin --password <PASSWORD> --insecure
```
📦 Argo CD Application (GitOps)
This project uses an Argo CD Application to deploy Kubernetes manifests stored in Git.
Example Application Manifest
```apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: root-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/keerthikhot/argocdwithk8s.git
    targetRevision: main
    path: k8s-kind-voting-app/k8s-specifications
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```
🔄 Sync Behavior
Automated Sync: Changes in Git are automatically applied to the cluster
Self-Heal: Manual changes in cluster are reverted
Prune: Deleted manifests in Git are removed from the cluster
🧪 Useful Commands
Check applications:
argocd app list
Sync application manually:
argocd app sync root-app
Check cluster status:
argocd cluster list

