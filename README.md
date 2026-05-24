# Kubernetes Project 3.2 — Resource Management and Horizontal Pod Autoscaling (HPA)

# Project Overview

This project demonstrates:

- Kubernetes Deployments
- Resource Requests and Limits
- Horizontal Pod Autoscaler (HPA)
- Load Simulation
- Automatic Pod Scaling

Platform:

- Google Kubernetes Engine (GKE)
- Docker Hub as image registry

---

# Project Architecture

```text
NGINX Application
        ↓
Kubernetes Deployment
        ↓
Resource Requests & Limits
        ↓
Horizontal Pod Autoscaler
        ↓
Automatic Scaling Based on CPU Usage
```

---

# Project Structure

```text
k8s-hpa-project/
│
├── index.html
├── Dockerfile
├── deployment.yaml
├── service.yaml
└── hpa.yaml
```

---

# Step 1 — Create Project Directory

```bash
mkdir k8s-hpa-project
cd k8s-hpa-project
```

---

# Step 2 — Create index.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>HPA Project</title>
</head>
<body style="text-align:center; margin-top:100px; font-family:Arial">

<h1>Kubernetes HPA Project</h1>

<h2>Autoscaling Demo Application</h2>

<p>This application scales automatically based on CPU usage.</p>

</body>
</html>
```

---

# Step 3 — Create Dockerfile

```dockerfile
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80
```

---

# Step 4 — Build Docker Image

```bash
docker build -t YOUR_DOCKERHUB_USERNAME/hpa-nginx:v1 .
```

---

# Step 5 — Login to Docker Hub

```bash
docker login
```

---

# Step 6 — Push Image to Docker Hub

```bash
docker push YOUR_DOCKERHUB_USERNAME/hpa-nginx:v1
```

---

# Step 7 — Verify GKE Cluster

```bash
kubectl get nodes
```

---

# Step 8 — Create Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: hpa-nginx-deployment

spec:
  replicas: 2

  selector:
    matchLabels:
      app: hpa-nginx

  template:
    metadata:
      labels:
        app: hpa-nginx

    spec:
      containers:
      - name: hpa-nginx-container
        image: YOUR_DOCKERHUB_USERNAME/hpa-nginx:v1

        ports:
        - containerPort: 80

        resources:
          requests:
            memory: "64Mi"
            cpu: "100m"

          limits:
            memory: "128Mi"
            cpu: "200m"
```

---

# Step 9 — Apply Deployment

```bash
kubectl apply -f deployment.yaml
```

---

# Step 10 — Verify Deployment

```bash
kubectl get deployments
kubectl get pods
```

---

# Step 11 — Verify Resource Allocation

```bash
kubectl describe pod POD_NAME
```

---

# Step 12 — Create Service YAML

```yaml
apiVersion: v1
kind: Service

metadata:
  name: hpa-nginx-service

spec:
  type: LoadBalancer

  selector:
    app: hpa-nginx

  ports:
  - port: 80
    targetPort: 80
```

---

# Step 13 — Apply Service

```bash
kubectl apply -f service.yaml
```

---

# Step 14 — Get External IP

```bash
kubectl get svc
```

---

# Step 15 — Install Metrics Server

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

---

# Step 16 — Verify Metrics Server

```bash
kubectl get deployment metrics-server -n kube-system
```

---

# Step 17 — Verify Metrics

```bash
kubectl top nodes
kubectl top pods
```

---

# Step 18 — Create Horizontal Pod Autoscaler

```bash
kubectl autoscale deployment hpa-nginx-deployment --cpu-percent=50 --min=2 --max=10
```

---

# Step 19 — Verify HPA

```bash
kubectl get hpa
```

---

# Step 20 — Simulate Application Load

```bash
kubectl run -i --tty load-generator --rm --image=busybox -- /bin/sh
```

Inside container:

```bash
while true; do wget -q -O- http://hpa-nginx-service; done
```

---

# Step 21 — Monitor Scaling

```bash
kubectl get hpa -w
kubectl get pods -w
```

---

# Step 22 — Stop Load Generator

Press:

```text
CTRL + C
```

---

# Important Commands

## Pods

```bash
kubectl get pods
```

## Deployments

```bash
kubectl get deployments
```

## Services

```bash
kubectl get svc
```

## HPA

```bash
kubectl get hpa
```

## Logs

```bash
kubectl logs POD_NAME
```

## Resource Usage

```bash
kubectl top pods
kubectl top nodes
```

## Delete Everything

```bash
kubectl delete all --all
```

---

# Expected Outcome

At the end of this project:

- Pods run with resource requests and limits
- Kubernetes schedules Pods efficiently
- HPA automatically scales Pods
- Application scales based on CPU usage
