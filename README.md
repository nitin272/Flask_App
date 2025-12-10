# Flask + MongoDB + Kubernetes Deployment

## 📌 Overview

This project demonstrates a complete microservice setup using:

* **Flask** (Python API)
* **MongoDB** (Database inside Kubernetes StatefulSet)
* **Docker** (Containerization)
* **Kubernetes** (Deployments, Services, StatefulSet, PVC, HPA)
* **Minikube** (Local Kubernetes cluster)

The application exposes 3 API routes:

* **GET /** → Health check
* **POST /data** → Insert JSON into MongoDB
* **GET /data** → Retrieve all MongoDB records

---

## 1️⃣ Local Setup

### Create virtual environment

```
python -m venv venv
venv\Scripts\activate
```

### Install dependencies

```
pip install -r requirements.txt
```

### Run Flask

```
set FLASK_APP=app.py
set FLASK_ENV=development
flask run
```

Application runs at:

```
http://127.0.0.1:5000
```

---

## 2️⃣ Docker Setup

### Build Docker image

```
docker build -t yourdockerhub/flask-mongo-app:latest .
```

### Push to Docker Hub

```
docker tag yourdockerhub/flask-mongo-app:latest nitin379/flask-mongo-app:latest
docker push nitin379/flask-mongo-app:latest
```

---

## 3️⃣ Kubernetes Setup

### Start Minikube

```
minikube start
minikube addons enable metrics-server
```

### Apply Kubernetes manifests

Inside the **k8s/** folder:

```
kubectl apply -f mongodb-secret.yaml
kubectl apply -f persistent-volume.yaml
kubectl apply -f persistent-volume-claim.yaml
kubectl apply -f mongodb-service.yaml
kubectl apply -f mongodb-statefulset.yaml
kubectl apply -f flask-deployment.yaml
kubectl apply -f flask-service.yaml
kubectl apply -f flask-hpa.yaml
```

---

## 4️⃣ Test Application Inside Kubernetes

### Get Flask Service URL

```
minikube service flask-service
```

This opens a NodePort URL like:

```
http://127.0.0.1:52878
```

### Test Endpoints

**GET /**

```
http://127.0.0.1:52878/
```

**POST /data** (JSON body)

```
{"name": "nitin"}
```

**GET /data** returns:

```
[
  {"name": "nitin"}
]
```

---

## 5️⃣ MongoDB StatefulSet + Storage

Uses MongoDB StatefulSet with:

* PersistentVolume (PV)
* PersistentVolumeClaim (PVC)
* hostPath storage (Minikube-compatible)

Storage path:

```
/mnt/data/mongo
```

StatefulSet ensures stable pod identity, like:

```
mongodb-0
```

---

## 6️⃣ Horizontal Pod Autoscaler (HPA)

Apply autoscaling:

```
kubectl apply -f flask-hpa.yaml
```

Check autoscaler:

```
kubectl get hpa
```

Example:

```
flask-hpa   Deployment/flask-app   0%/70%   2   5   2
```

Autoscaler scales between **2 and 5 pods** based on CPU usage.

---

## 7️⃣ Kubernetes DNS Explanation

Kubernetes automatically assigns DNS names to services.
MongoDB is reachable at:

```
mongodb-service.default.svc.cluster.local
```

Flask connects via:

```
mongodb://mongo-user:mongo-pass@mongodb-service:27017/
```

This resolves internally to the MongoDB pod.

---

## 8️⃣ Project Structure

```
flask-mongodb-app/
│── app.py
│── requirements.txt
│── Dockerfile
│── README.md
│── k8s/
│     ├── mongodb-secret.yaml
│     ├── mongodb-service.yaml
│     ├── mongodb-statefulset.yaml
│     ├── persistent-volume.yaml
│     ├── persistent-volume-claim.yaml
│     ├── flask-deployment.yaml
│     ├── flask-service.yaml
│     ├── flask-hpa.yaml
```

---

## ✅ Final Notes

This project demonstrates:

* Kubernetes Deployments & StatefulSets
* Persistent storage using PV/PVC
* Autoscaling with HPA
* Dockerized Flask application
* Internal service discovery using DNS
* Working cloud‑native microservice architecture

Your application is fully functional and production‑ready.

---
