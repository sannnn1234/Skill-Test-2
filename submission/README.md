# SKILL TEST 2


## Architecture Overview
| Service | Port | Type | Description |
|----------|------|------|-------------|
| user-service | 3000 | ClusterIP | Returns list of users |
| product-service | 3001 | ClusterIP | Returns list of products |
| order-service | 3002 | ClusterIP | Creates and lists orders |
| gateway-service | 3003 | NodePort | Proxies all services |

The gateway service uses Kubernetes for internal communication:
- http://user-service:3000
- http://product-service:3001
- http://order-service:3002


---

# Step 1 — Start Minikube

```powershell
minikube start --driver=docker
```

Verify cluster:

```powershell
minikube status
kubectl get nodes
```
---
![Local Setup](https://raw.githubusercontent.com/sannnn1234/Skill-Test-2/main/submission/screenshots/minikube.png)


---

# Step 2 — Configure Docker Environment for Minikube

For PowerShell:

```powershell
minikube docker-env --shell powershell | Invoke-Expression
```

Verify:

```powershell
docker images
```

---

# Step 3 — Build Docker Images

```powershell
docker build -t user-service:latest    ./services/user-service/
docker build -t product-service:latest ./services/product-service/
docker build -t order-service:latest   ./services/order-service/
docker build -t gateway-service:latest ./services/gateway-service/
```
---
![Local Setup](https://raw.githubusercontent.com/sannnn1234/Skill-Test-2/main/submission/screenshots/docker.png)

Verify images:

```powershell
minikube image ls
```

---

# Step 4 — Deploy Kubernetes Resources

Apply deployments:

```powershell
kubectl apply -f deployments/
```

Apply services:

```powershell
kubectl apply -f services/
```

Apply ingress (optional):

```powershell
kubectl apply -f ingress/
```

---

# Step 5 — Verify Pods and Services

```powershell
kubectl get pods
kubectl get svc
```

Expected:
![Local Setup](https://raw.githubusercontent.com/sannnn1234/Skill-Test-2/main/submission/screenshots/pods.png)

---

# Step 6 — Port Forward Services

## User Service

```powershell
kubectl port-forward svc/user-service 3000:3000
```

Test:

```powershell
curl.exe http://localhost:3000/users
```

---

## Product Service

```powershell
kubectl port-forward svc/product-service 3001:3001
```

Test:

```powershell
curl.exe http://localhost:3001/products
```

---

## Order Service

```powershell
kubectl port-forward svc/order-service 3002:3002
```

Test:

```powershell
 http://localhost:3002/orders
```

---

## Gateway Service

```powershell
kubectl port-forward svc/gateway-service 3003:3003
```

Test:

```powershell
 http://localhost:3003/api/users
 http://localhost:3003/api/products
 http://localhost:3003/api/orders
```

---

# Step 7 — Verify Inter-Service Communication

Check logs:

```powershell
kubectl logs deployment/gateway-service
```

Check DNS resolution:

```powershell
kubectl exec deployment/gateway-service -- nslookup user-service
```

Check communication from gateway pod:

```powershell
kubectl exec deployment/gateway-service -- wget -qO- http://user-service:3000/users
kubectl exec deployment/gateway-service -- wget -qO- http://product-service:3001/products
kubectl exec deployment/gateway-service -- wget -qO- http://order-service:3002/orders
```

---

# Step 8 — Create Order

```powershell
curl.exe -X POST http://localhost:3003/api/orders `
-H "Content-Type: application/json" `
-d "{\"userId\":1,\"productId\":2}"
```

Verify:



---

# Step 9 — Health Check
```
 http://localhost:3000/health
 http://localhost:3001/health
 http://localhost:3002/health
 http://localhost:3003/health
```

---

# Bonus — Ingress Setup

Enable ingress:

```powershell
minikube addons enable ingress
```

Verify:

```powershell
kubectl get pods -n ingress-nginx
```

Apply ingress:
```powershell
kubectl apply -f ingress/ingress.yaml
```

Get Minikube IP:
```powershell
minikube ip
```

Test ingress:

```powershell
curl.exe http://microservices.local/api/users
curl.exe http://microservices.local/api/products
curl.exe http://microservices.local/api/orders
```

---

