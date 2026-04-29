# Project 4: Deploy Prometheus and Grafana on Kubernetes (Amazon EKS) using Terraform & Helm

## Project Overview

This project deploys a **complete monitoring solution** using Prometheus and Grafana on a Kubernetes cluster provisioned with Amazon EKS. Infrastructure is managed as code using Terraform, and applications are deployed using Helm charts.

**GitHub Repository:** [Kubernetes Monitoring Stack on Amazon EKS](https://github.com/itzsujal7/Kubernetes-Monitoring-Stack-on-Amazon-EKS-using-Terraform-Helm.git)

---

## Data Flow

```
Kubernetes Cluster
        ↓
Prometheus (scrapes & collects metrics)
        ↓
Grafana (visualizes dashboards)
```

---

## Infrastructure Components

| Component      | Purpose                            |
|----------------|------------------------------------|
| Terraform      | Infrastructure provisioning (IaC)  |
| Amazon EKS     | Managed Kubernetes cluster         |
| Kubernetes     | Container orchestration            |
| Helm           | Application deployment             |
| Prometheus     | Metrics collection                 |
| Grafana        | Dashboard visualization            |

---

## Deployment Steps

### Prerequisites
Install the following on your local machine:
- Terraform
- kubectl
- Helm
- AWS CLI (configured with credentials)

### Step 1 — Provision EKS Cluster with Terraform
```bash
terraform init
terraform plan
terraform apply
```

### Step 2 — Configure kubectl
```bash
aws eks update-kubeconfig --region <region> --name <cluster-name>
```

### Step 3 — Create Monitoring Namespace
```bash
kubectl create namespace monitoring
```

### Step 4 — Deploy Prometheus using Helm
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/prometheus -n monitoring
```

### Step 5 — Deploy Grafana using Helm
```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm install grafana grafana/grafana -n monitoring
```

### Step 6 — Access Grafana
```bash
# Port-forward Grafana to localhost
kubectl port-forward svc/grafana 3000:80 -n monitoring

# Retrieve the default admin password
kubectl get secret grafana -n monitoring -o jsonpath="{.data.admin-password}" | base64 --decode
```

### Step 7 — Connect Prometheus as Grafana Data Source
1. Open Grafana at `http://localhost:3000`
2. Go to **Configuration → Data Sources → Add data source**
3. Select **Prometheus**
4. Set URL: `http://prometheus-server.monitoring.svc.cluster.local`
5. Import a pre-built Kubernetes dashboard (e.g., Dashboard ID `3119`)

---

## Benefits of Using Terraform + Helm Together

- **Infrastructure as Code** — no manual AWS setup
- **Consistent and repeatable** deployments
- **Easy scaling and updates** via Helm chart values
- **Faster setup** with community Helm charts
- **Version-controlled** infrastructure

---

## Challenges & Solutions

| Challenge | Solution |
|-----------|----------|
| Cluster connection issues | Resolved using `aws eks update-kubeconfig` |
| Helm installation errors | Fixed by running `helm repo update` |
| Grafana login failure | Retrieved password from Kubernetes secret |

---

## Outcome

Successfully deployed a real-time monitoring system that collects Kubernetes cluster metrics with Prometheus and visualizes them through Grafana dashboards — all on a fully automated, Terraform-managed EKS cluster.
