# CI/CD Demo with Java, ArgoCD, and GitHub Actions

This project demonstrates a complete CI/CD pipeline following industry best practices.

## Prerequisites
- Local Kubernetes cluster (Docker Desktop, Minikube, or Kind).
- GitHub Repository with your code pushed.
- GitHub Personal Access Token (PAT) with `repo` and `write:packages` scopes.

## Architecture
1. **Java App**: Spring Boot REST API.
2. **CI Pipeline (GitHub Actions)**:
   - Build and Test.
   - Containerization (Docker).
   - Security Scanning (Trivy).
   - Push to GHCR (GitHub Container Registry).
   - GitOps: Updates K8s manifests in the repository.
3. **CD Pipeline (ArgoCD)**:
   - Monitors the `k8s/` directory.
   - Automatically syncs changes to the cluster.
   - Environments: `dev` (Auto), `stg` (Manual), `prod` (Manual).

## How to Set Up

### 1. Push to GitHub
Initialize your repo and push the code:
```bash
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
git push -u origin main
```

### 2. Configure GitHub Environments
To enable manual approvals for Staging and Production:
1. Go to your GitHub Repository -> **Settings** -> **Environments**.
2. Click **New environment** and name it `stg`.
3. Check **Required reviewers** and add yourself.
4. Repeat for `prod`.

### 3. ArgoCD Access
ArgoCD is installed in the `argocd` namespace. To access the UI:
```bash
kubectl port-forward svc/argocd-server -n argocd 8081:443
```
Open `https://localhost:8081`. 
- **Username**: `admin`
- **Password**: Get it with `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`

## How it works
- **Dev**: On every push to `main`, the pipeline updates `k8s/dev/app.yaml`. ArgoCD detects the change and deploys it immediately.
- **Staging/Prod**: The pipeline waits for your manual approval in the GitHub Actions tab. Once approved, it updates the respective manifest, and ArgoCD syncs it.
