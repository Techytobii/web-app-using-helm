# 📘 Deploying a Web Application Using Helm in Kubernetes with Jenkins CI/CD

## 📌 Introduction

This project demonstrates how to deploy a web application to Kubernetes using **Helm** and automate the process with **Jenkins**. The workflow covers:

- Creating a Docker image of the application
- Pushing the image to a container registry
- Using Helm to manage Kubernetes deployments
- Automating the build and deployment with a Jenkins pipeline

## 📌 Project Objectives
The main goals of this project are:
1. **Automate** the build, test, and deployment process using **Jenkins**.
2. **Containerize** the application using **Docker** for consistency across environments.
3. **Deploy** the application to **Kubernetes** using automated manifests.
4. Enable **Continuous Integration & Continuous Deployment (CI/CD)** workflows.
5. Practice industry-standard DevOps tools and workflows.

---

## 📂 GitHub Repository
🔗 **Repository URL:** [https://github.com/Techytobii/web-app-using-helm.git](https://github.com/username/repo-name)


## 📄 Overview

**Workflow Steps:**
1. Developer pushes code to Git repository.
2. Jenkins pipeline triggers on new commit.
3. Docker image is built and pushed to registry.
4. Helm deploys/updates the Kubernetes resources.
5. Application becomes accessible via LoadBalancer/Ingress.

---

## ✅ Prerequisites

- Kubernetes cluster (Minikube, Kind, EKS, GKE, AKS, etc.)
- `kubectl` installed and configured
- Docker installed
- Helm installed
- Jenkins server with:
  - DockerHub credentials
  - kubeconfig credentials
  - Git integration
- Git repository containing application source code and Helm chart

🖼️ ![kubectl-get-nodes](./img/kubectl-get-nodes.png)
`kubectl get nodes`

---

## 🛠 Project Structure

```plaintext
web-app-using-helm/
│
├── helm-web-app/           # Helm chart folder
│   ├── charts/
│   ├── templates/
│   │   ├── _helpers.tpl
│   │   ├── deployment.yaml
│   │   ├── hpa.yaml
│   │   ├── ingress.yaml
│   │   ├── service.yaml
│   │   ├── serviceaccount.yaml
│   │   └── tests/
│   ├── .helmignore
│   ├── Chart.yaml
│   └── values.yaml
│
├── img/                    # Images or static assets
├── Jenkinsfile             # Jenkins pipeline configuration
├── Dockerfile              # Docker build instructions
├── README.md
└── .gitignore
````

🖼️ ![project-tree](./img/project-tree.png)

---

## 🐳 Dockerfile

**Node.js Example**

```dockerfile
# Use official Node.js image
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install --production

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

![docker-build-output](./img/docker-build-output.png)
---

## 📦 Helm Deployment File (`helm-web-app/templates/deployment.yaml`)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "helm-web-app.fullname" . }}
  labels:
    app: {{ include "helm-web-app.name" . }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ include "helm-web-app.name" . }}
  template:
    metadata:
      labels:
        app: {{ include "helm-web-app.name" . }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - containerPort: {{ .Values.service.port }}
          resources:
            {{- toYaml .Values.resources | nindent 12 }}
```

---

## 🔄 Jenkinsfile

```groovy
pipeline {
    agent any

    environment {
        DOCKERHUB_USER = credentials('dockerhub-username')
        DOCKERHUB_PASS = credentials('dockerhub-password')
        DOCKER_IMAGE = "mydockerhubuser/my-webapp"
        KUBE_CONFIG = credentials('kubeconfig-credential')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/user/web-app-using-helm.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin'
                sh 'docker push $DOCKER_IMAGE:$BUILD_NUMBER'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig-credential', variable: 'KUBECONFIG_FILE')]) {
                    sh '''
                        export KUBECONFIG=$KUBECONFIG_FILE
                        helm upgrade --install web-app ./helm-web-app \
                            --set image.repository=$DOCKER_IMAGE \
                            --set image.tag=$BUILD_NUMBER
                    '''
                }
            }
        }
    }
}
```


---

## 🚀 Deployment

**Manual Helm deploy:**

```bash
helm install web-app ./helm-web-app
```

**View status:**

```bash
helm list
kubectl get pods
kubectl get svc
```

 ![kubectl-get-pods](./img/kubectl-get-pods.png)

`kubectl get pods`
Kubernetes pods running

`kubectl get svc`

![kubectl-get-svc](./img/kubectl-get-svc.png)
Kubernetes service details
---
## Nginx Web Output

![nginx-web-output](./img/nginx-website.png)
---

## 🧹 Cleanup

```bash
helm uninstall web-app
```
![helm-install](./img/helm-uninstall.png)

---

## ⚠ Challenges Faced

During the project, the following issues were encountered and resolved:

1. **Helm Template Errors**

   * Cause: Incorrect indentation in `deployment.yaml`.
   * Solution: Used `nindent` in Helm templates to fix YAML formatting.

2. **Image Pull Failures**

   * Cause: Docker image not pushed before Helm deployment.
   * Solution: Reordered Jenkins pipeline to push image before `helm upgrade`.

3. **Kubeconfig Authentication Issues**

   * Cause: Jenkins unable to connect to Kubernetes cluster.
   * Solution: Stored kubeconfig file as Jenkins credential and exported in deploy stage.

4. **LoadBalancer Delay**

   * Cause: Cloud provider provisioning delay.
   * Solution: Waited for external IP to be assigned and verified service.

---

## 📓 Conclusion

By combining **Helm** and **Jenkins**, we automated the build and deployment process for a Kubernetes application. This approach ensures:

* Consistency in deployments
* Versioned and repeatable application releases
* Minimal manual intervention

This setup is suitable for production-grade CI/CD pipelines with Kubernetes.
```