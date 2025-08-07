# 📘 Deploying a Web Application Using Helm in Kubernetes

## 📌 Introduction

In this guide, you'll learn how to deploy a web application to a Kubernetes cluster using **Helm**, the package manager for Kubernetes. Helm helps you define, install, and upgrade even the most complex Kubernetes applications using Helm Charts.

![Helm Architecture Diagram](image-placeholder)

## 📄 Overview

This project involves:
- Installing Helm on your local machine
- Creating a Helm Chart for a web application
- Deploying the chart to a Kubernetes cluster
- Verifying the deployment

![Helm Workflow](image-placeholder)

## ✅ Prerequisites

- Basic understanding of Kubernetes
- A working Kubernetes cluster (Minikube, Kind, or any cloud provider)
- kubectl installed and configured
- Internet connection
- A sample web application (e.g., a simple Node.js or Python app)

![kubectl status](image-placeholder)

# 🧰 Step 1: Install Helm

## 🐧 For Linux and macOS Users

Install Helm using Homebrew:

```bash
brew install helm
```

Or via script:

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

Verify installation:

```bash
helm version
```

![Terminal Helm Version](image-placeholder)

## 🪟 For Windows Users

Using Chocolatey:

```powershell
choco install kubernetes-helm
```

Or use the official Windows binary from the [Helm GitHub Releases](https://github.com/helm/helm/releases).

Verify installation:

```powershell
helm version
```

![Windows Helm Install](image-placeholder)

# 🛠️ Step 2: Create a New Helm Chart

Create a new chart using the Helm CLI:

```bash
helm create my-webapp
```

This will generate a folder structure like:

```
my-webapp/
├── charts/
├── templates/
├── values.yaml
└── Chart.yaml
```

![Helm Chart Folder Structure](image-placeholder)

## 🧾 Customize Your Chart

Edit the `values.yaml` file to define your container image, ports, replica count, and other configuration options.

Example:

```yaml
replicaCount: 2
image:
  repository: mydockerhubuser/my-webapp
  tag: latest
service:
  type: LoadBalancer
  port: 80
```

![Edited values.yaml](image-placeholder)

## 🚀 Deploy the Chart

Use the following command to deploy your app to the cluster:

```bash
helm install my-webapp ./my-webapp
```

To view the status:

```bash
helm list
kubectl get all
```

![Helm List and Pods](image-placeholder)

## 🔄 Upgrade and Rollback

To upgrade your chart:

```bash
helm upgrade my-webapp ./my-webapp
```

To rollback:

```bash
helm rollback my-webapp 1
```

![Helm Rollback Diagram](image-placeholder)

## ✅ Validate Deployment

Ensure the service is reachable:

```bash
kubectl get svc
```

Access your app via the LoadBalancer IP or NodePort.

![Browser Showing App](image-placeholder)

# 🧹 Clean Up

To remove the deployment:

```bash
helm uninstall my-webapp
```

![Helm Uninstall](image-placeholder)

# 📓 Conclusion

Using Helm simplifies Kubernetes deployments by abstracting configuration into reusable, versioned charts. This project demonstrates a foundational Helm workflow for real-world Kubernetes application deployments.
