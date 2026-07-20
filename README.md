# Local GitOps & Platform Engineering Lab (CNPA Prep)

A hands-on proof-of-concept demonstrating modern **Platform Engineering** and **GitOps principles** to build an automated Continuous Delivery (CD) pipeline. This lab maps directly onto the core domains of the Linux Foundation's **Certified Cloud Native Platform Engineering Associate (CNPA)** track.

## 🎯 Project Objective
The goal of this project is to shift away from manual, imperative application deployments and implement a fully automated **Internal Developer Platform (IDP)** lifecycle. By leveraging declarative configurations, the system automatically reconciles the live cluster state with the code defined in this repository, ensuring absolute consistency and zero configuration drift.

## 🏗️ Architecture & Component Stack
*   **Orchestration Engine:** Kubernetes (Local multi-node cluster running via Docker Desktop)
*   **GitOps Controller:** ArgoCD (Deployed natively within the cluster control plane)
*   **Source of Truth:** GitHub (Hosting declarative configurations)
*   **Target Application:** Nginx Web Server (Containerized microservice)

---

## 🧠 Core Platform Engineering Concepts Demonstrated

### 1. Declarative Configuration vs. Imperative Commands
Instead of running manual execution scripts (like `kubectl run`), the entire target architecture is defined as plain text manifests inside the `/apps` directory. The platform is told the *desired final state*, and it automates the deployment path independently.

### 2. The Reconciliation Loop
ArgoCD acts as the primary controller loop, constantly comparing the live infrastructure (Actual State) against this GitHub repository (Desired State). When changes are pushed to Git, the loop automatically triggers a rolling update to synchronize the environment.

### 3. Automated Self-Healing & Drift Correction
The platform inherently prevents manual system tampering or environment failures. If an individual pod or resource is manually altered or deleted via a CLI bypass, the reconciliation engine instantly detects the drift and re-provisions the exact resources required to maintain target health.

---

## 🚀 How to Replicate This Lab

### Prerequisites
*   Docker Desktop (with the built-in Kubernetes engine activated)
*   `kubectl` CLI tool installed locally

### Step 1: Install the Management Plane
Deploy the ArgoCD framework into an isolated control namespace inside your cluster:
\`\`\`bash
kubectl create namespace argocd
kubectl apply -n argocd --server-side --force-conflicts -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
\`\`\`

### Step 2: Deploy the Declarative GitOps Application
Instead of using a web interface, bootstrap the automated delivery loop by applying the custom application resource directly from your local environment:
\`\`\`bash
kubectl apply -f platform/argocd-app.yaml
\`\`\`

### Step 3: Verify Cluster Status
Check that the reconciliation engine has successfully spun up your targeted application replicas:
\`\`\`bash
kubectl get deployments -n default
kubectl get pods -n default
\`\`\`
