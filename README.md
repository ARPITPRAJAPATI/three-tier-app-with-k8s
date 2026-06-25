# 🚀 Three-Tier Chat Application on Kubernetes

A full-stack real-time chat application deployed on **Kubernetes** using a complete **3-tier architecture** with:

* **Frontend**: React + Nginx
* **Backend**: Node.js + Express
* **Database**: MongoDB
* **Containerization**: Docker
* **Orchestration**: Kubernetes
* **Networking**: Services + Ingress
* **Storage**: PersistentVolume + PersistentVolumeClaim
* **Secrets**: Kubernetes Secret

---

## 📌 Project Overview

This project demonstrates how to deploy a production-style 3-tier web application on Kubernetes.

The application is divided into three layers:

```text
User Browser
   ↓
Ingress Controller
   ↓
Frontend Service
   ↓
Frontend Pod
   ↓
Backend Service
   ↓
Backend Pod
   ↓
MongoDB Service
   ↓
MongoDB Pod + Persistent Storage
```

---

## 🧱 Architecture

```text
                    ┌────────────────────┐
                    │    User Browser     │
                    │  chat-aru.com       │
                    └─────────┬──────────┘
                              │
                              ▼
                    ┌────────────────────┐
                    │  Ingress Controller │
                    │      NGINX          │
                    └─────────┬──────────┘
                              │
              ┌───────────────┴───────────────┐
              ▼                               ▼
   ┌────────────────────┐          ┌────────────────────┐
   │  Frontend Service  │          │  Backend Service   │
   │      Port 80       │          │     Port 5001      │
   └─────────┬──────────┘          └─────────┬──────────┘
             │                               │
             ▼                               ▼
   ┌────────────────────┐          ┌────────────────────┐
   │  Frontend Pod      │          │  Backend Pod       │
   │ React + Nginx      │          │ Node.js + Express  │
   └────────────────────┘          └─────────┬──────────┘
                                             │
                                             ▼
                                  ┌────────────────────┐
                                  │ MongoDB Service    │
                                  │    Port 27017      │
                                  └─────────┬──────────┘
                                            │
                                            ▼
                                  ┌────────────────────┐
                                  │ MongoDB Pod        │
                                  │ Persistent Storage │
                                  └────────────────────┘
```

---

## 🛠️ Tech Stack

| Layer            | Technology               |
| ---------------- | ------------------------ |
| Frontend         | React, Nginx             |
| Backend          | Node.js, Express         |
| Database         | MongoDB                  |
| Containerization | Docker                   |
| Orchestration    | Kubernetes               |
| Local Cluster    | Minikube                 |
| Ingress          | NGINX Ingress Controller |
| Storage          | PV, PVC                  |
| Secrets          | Kubernetes Secret        |

---

## 📁 Project Structure

```text
three-tier-app-with-k8s/
│
├── backend/
│   ├── src/
│   ├── package.json
│   └── Dockerfile
│
├── frontend/
│   ├── src/
│   ├── package.json
│   └── Dockerfile
│
├── k8s/
│   ├── namespace.yml
│   ├── secret.yml
│   ├── mongodb-pv.yml
│   ├── mongodb-pvc.yml
│   ├── mongodb-deployment.yml
│   ├── mongodb-service.yml
│   ├── backend-deployment.yml
│   ├── backend-service.yml
│   ├── frontend-deployment.yml
│   ├── frontend-service.yml
│   └── ingress.yml
│
├── docker-compose.yml
├── Jenkinsfile
└── README.md
```

---

## ⚙️ Kubernetes Components Used

### Namespace

All application resources are deployed inside the `chat-app` namespace.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: chat-app
```

---

### MongoDB Persistent Storage

MongoDB uses:

* `PersistentVolume`
* `PersistentVolumeClaim`

This ensures MongoDB data is stored persistently even if the MongoDB pod restarts.

---

### Backend Deployment

The backend runs as a Node.js container and connects to MongoDB using:

```text
mongodb-service:27017
```

Example MongoDB URI:

```text
mongodb://mongoadmin:secret@mongodb-service:27017/chatapp?authSource=admin
```

---

### Frontend Deployment

The frontend is served using Nginx and exposed internally through a Kubernetes Service.

---

### Ingress

Ingress exposes the application using a custom local domain:

```text
chat-aru.com
```

Ingress routes:

```text
/      → frontend service
/api   → backend service
```

---

## 🚀 How to Run This Project Locally

### 1. Start Minikube

```bash
minikube start
```

---

### 2. Enable Ingress Controller

```bash
minikube addons enable ingress
```

Check Ingress controller:

```bash
kubectl get pods -n ingress-nginx
```

---

### 3. Apply Kubernetes Manifests

From the project root:

```bash
kubectl apply -f k8s/
```

---

### 4. Check Pods

```bash
kubectl get pods -n chat-app
```

Expected output:

```text
NAME                                  READY   STATUS    RESTARTS   AGE
backend-deployment-xxxxx              1/1     Running   0          1m
frontend-deployment-xxxxx             1/1     Running   0          1m
mongodb-deployment-xxxxx              1/1     Running   0          1m
```

---

### 5. Check Services

```bash
kubectl get svc -n chat-app
```

---

### 6. Check Ingress

```bash
kubectl get ingress -n chat-app
```

Expected:

```text
NAME              CLASS   HOSTS          ADDRESS        PORTS
chatapp-ingress   nginx   chat-aru.com   192.168.x.x    80
```

---

## 🌐 Access Application Using Custom Domain

Add this entry in your Windows hosts file:

```text
127.0.0.1 chat-aru.com
```

Hosts file location:

```text
C:\Windows\System32\drivers\etc\hosts
```

Flush DNS:

```powershell
ipconfig /flushdns
```

Verify:

```powershell
ping chat-aru.com
```

Expected:

```text
Pinging chat-aru.com [127.0.0.1]
```

---

### Port Forward Ingress Controller

```bash
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 80:80
```

Now open:

```text
http://chat-aru.com
```

---

## 🔐 Kubernetes Secret

The project uses Kubernetes Secret for storing sensitive values.

Example:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: chatapp-secret
  namespace: chat-app
type: Opaque
data:
  password: <base64-encoded-password>
```

To encode a value:

```bash
echo -n "your-password" | base64
```

---

## 🧪 Useful Debugging Commands

### Check pods

```bash
kubectl get pods -n chat-app
```

### Check logs

```bash
kubectl logs -n chat-app -l app=backend
kubectl logs -n chat-app -l app=frontend
kubectl logs -n chat-app -l app=mongodb
```

### Describe pod

```bash
kubectl describe pod <pod-name> -n chat-app
```

### Check services

```bash
kubectl get svc -n chat-app
```

### Check endpoints

```bash
kubectl get endpoints -n chat-app
```

### Check ingress

```bash
kubectl describe ingress chatapp-ingress -n chat-app
```

---

## 🧠 Issues Faced and Fixed

During deployment, several real-world Kubernetes issues were debugged and fixed:

* Wrong YAML fields like `matchLables`, `port`, and `accessMode`
* Namespace-related resource issues
* Missing MongoDB PVC
* MongoDB authentication failure
* Backend missing environment variables
* Wrong MongoDB service DNS
* Frontend Nginx upstream service issue
* Ingress host mismatch
* Windows hosts file DNS issue
* Port forwarding from local system to Ingress controller

---

## ✅ Final Working Flow

```text
chat-aru.com
   ↓
127.0.0.1
   ↓
kubectl port-forward 80:80
   ↓
NGINX Ingress Controller
   ↓
chatapp-ingress
   ↓
Frontend Service
   ↓
Frontend Pod
   ↓
Backend Service
   ↓
Backend Pod
   ↓
MongoDB Service
   ↓
MongoDB Pod
   ↓
Persistent Volume
```

---

## 🧹 Cleanup

To delete all resources:

```bash
kubectl delete namespace chat-app
```

To disable Minikube ingress:

```bash
minikube addons disable ingress
```

To stop Minikube:

```bash
minikube stop
```

---

## 📌 Key Learning

This project helped me understand how a real-world application is deployed on Kubernetes using:

* Deployments
* Services
* Namespaces
* Secrets
* Persistent Volumes
* Persistent Volume Claims
* Ingress
* Local DNS mapping
* Debugging pod crashes
* Service discovery inside Kubernetes

---

## 👨‍💻 Author

**Arpit Kumar Prajapati**

* GitHub: [ARPITPRAJAPATI](https://github.com/ARPITPRAJAPATI)
* Project: Three-Tier Chat Application with Kubernetes

---

## 📄 License

This project is licensed under the MIT License.
