Got it ✅
Here’s the **full documentation** in clean Markdown format — ready for **copy & paste** into your repo’s `README.md`.
I’ve added **image/code placeholders** and a **Challenges Faced** section at the end.

---

````markdown
# 📘 Deploying a Web Application Using Helm in Kubernetes with Jenkins CI/CD

## 📌 Introduction

This project demonstrates how to deploy a web application to Kubernetes using **Helm** and automate the process with **Jenkins**. The workflow covers:

- Creating a Docker image of the application
- Pushing the image to a container registry
- Using Helm to manage Kubernetes deployments
- Automating the build and deployment with a Jenkins pipeline

🖼️ *Image Placeholder:* Diagram showing Jenkins → Helm → Kubernetes pipeline

---

## 📄 Overview

**Workflow Steps:**
1. Developer pushes code to Git repository.
2. Jenkins pipeline triggers on new commit.
3. Docker image is built and pushed to registry.
4. Helm deploys/updates the Kubernetes resources.
5. Application becomes accessible via LoadBalancer/Ingress.

🖼️ *Image Placeholder:* Pipeline stages diagram

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

🖼️ *Screenshot Placeholder:* Output of `kubectl get nodes`

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

🖼️ *Screenshot Placeholder:* VS Code file tree

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

**Python Example**

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000
CMD ["python", "app.py"]
```

🖼️ *Screenshot Placeholder:* Docker build logs

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

🖼️ *Screenshot Placeholder:* Helm install output

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

🖼️ *Screenshot Placeholder:* Jenkins build pipeline view

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

🖼️ *Screenshot Placeholder:* Kubernetes pods running

---

## 🧹 Cleanup

```bash
helm uninstall web-app
```

🖼️ *Screenshot Placeholder:* Helm uninstall output

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

🖼️ *Screenshot Placeholder:* Error log screenshot and solution steps

---

## 📓 Conclusion

By combining **Helm** and **Jenkins**, we automated the build and deployment process for a Kubernetes application. This approach ensures:

* Consistency in deployments
* Versioned and repeatable application releases
* Minimal manual intervention

This setup is suitable for production-grade CI/CD pipelines with Kubernetes.

🖼️ *Image Placeholder:* Final architecture diagram showing end-to-end flow

```

---

Do you want me to now also prepare a **diagram (Jenkins → Docker → Helm → Kubernetes)** that you can drop in as the architecture screenshot? That would make this README visually stronger.
```
