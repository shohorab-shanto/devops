# Installing Argo CD on Minikube

Follow these steps to install Argo CD on a Minikube cluster:

## 1. Start Minikube

```sh
minikube start
```

## 2. Create a Namespace for Argo CD

```sh
kubectl create namespace argocd
```

## 3. Install Argo CD

```sh
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## 4. Expose the Argo CD API Server

For local access, use port-forwarding:

```sh
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Now, Argo CD UI is accessible at [https://localhost:8080](https://localhost:8080).

## 5. Get the Initial Admin Password

```sh
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
```

Use `admin` as the username and the above password to log in.

Apply `kubectl apply -f Application.yml` then go to your Argo Cd Web ui and check.