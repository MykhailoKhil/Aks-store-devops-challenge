# Aks-store-devops-challenge

This project demonstrates how to deploy the AKS Store Demo application locally on Docker Desktop Kubernetes with HTTPS support using a self-signed TLS certificate and NGINX Ingress.

---

## 🚀 Prerequisites

- Docker Desktop with Kubernetes enabled
- [`kubectl`](https://kubernetes.io/docs/tasks/tools/)
- [`helm`](https://helm.sh/)
- [`openssl`](https://www.openssl.org/)

---

## 🛠 Setup Steps

### 1. Clone the Repository

```bash
git clone https://github.com/Azure-Samples/aks-store-demo.git

```

### 2. Deploy the Application
```bash
kubectl apply -f aks-store-demo/aks-store-quickstart.yaml

```

### 3. Install NGINX Ingress Controller
```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx --create-namespace

```

### 4. Add store.example.com to /etc/hosts 
```bash
sudo echo "127.0.0.1 store.local order.store.local product.store.local rabbitmq.store.local" | sudo tee -a /etc/hosts

```

### 5. Apply the Ingress Resource

```bash
kubectl apply -f kubernetes-manifests/ingress.yaml

```

### 6. Generate Self-Signed TLS Certificate
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout tls.key -out tls.crt \
  -subj "/CN=store.local/O=store.local"

```

### 7. Create TLS Secret in Kubernetes
```bash
kubectl create secret tls store-local-tls \
  --cert=tls.crt \
  --key=tls.key

```

### 8. Port-Forward to Access Ingress
```bash
kubectl port-forward svc/ingress-nginx-controller -n ingress-nginx 8443:443


```

How you can open browuser and open page http://store.example.com/