# Tyk Istio Demo

## Requirements
1. [minikube](https://minikube.sigs.k8s.io/docs/start/)
2. [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)
3. [helm](https://helm.sh/docs/intro/install/)

## Installation

### Minikube

Start Minikube
```
minikube start
minikube addons enable ingress
```

### ArgoCD

Install ArgoCD on Minikube

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

You can expose ArgoCD UI using the following command:
```
kubectl port-forward svc/argocd-server --namespace argocd 8443:443
```

You can access the Keycloak instance in your browser at [localhost:8443](http://localhost:8443):
```
Username: admin
```

You can get the ArgoCD admin password by running the following command:
```
kubectl get secrets -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### Istio
```
istioctl install -y
```

### Install ArgoCD Apps

The apps that will be installed are:
- Tyk
- Tyk Operator
- HttpBin Deployment and service
- Keycloak

Install Apps

```
kubectl apply -f apps
```

You can expose the Tyk Dashboard and Gateway to your localhost using the following commands:
```
kubectl port-forward svc/dashboard-svc-tyk-stack-tyk-pro --namespace tyk 3000
kubectl port-forward svc/gateway-svc-tyk-stack-tyk-pro --namespace tyk 8080
```

You can access the Tyk Dashboard instance in your browser at [localhost:3000](http://localhost:3000):
```
Username: default@example.com
Password: topsecretpassword
```

You can expose Keycloak to your localhost using the following command:
```
kubectl port-forward svc/keycloak-service --namespace keycloak 7000
```

You can access the Keycloak instance in your browser at [localhost:7000](http://localhost:7000):
```
Username: default@example.com
Password: topsecretpassword
```

Generate JWT from Keycloak:
```
curl -L -s -X POST 'http://localhost:7000/realms/keycloak-oauth/protocol/openid-connect/token' \
   -H 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'client_id=keycloak-oauth' \
   --data-urlencode 'grant_type=password' \
   --data-urlencode 'client_secret=NoTgoLZpbrr5QvbNDIRIvmZOhe9wI0r0' \
   --data-urlencode 'scope=openid' \
   --data-urlencode 'username=admin@example.com' \
   --data-urlencode 'password=topsecretpassword' | jq -r '.access_token'
```
or

```
curl -L -s -X POST 'http://localhost:7000/realms/keycloak-oauth/protocol/openid-connect/token' \
   -H 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'client_id=keycloak-oauth' \
   --data-urlencode 'grant_type=password' \
   --data-urlencode 'client_secret=NoTgoLZpbrr5QvbNDIRIvmZOhe9wI0r0' \
   --data-urlencode 'scope=openid' \
   --data-urlencode 'username=developer@example.com' \
   --data-urlencode 'password=topsecretpassword' | jq -r '.access_token'
```
