# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

### Common Commands

1. **Install School App**
   - Setup MongoDB:
     ```bash
     kubectl create ns schoolapp
     helm repo add bitnami https://charts.bitnami.com/bitnami
     helm install schoolapp-mongodb --namespace schoolapp --set auth.enabled=true --set auth.rootUser=schoolapp --set auth.rootPassword=mongoRootPass bitnami/mongodb
     ```
   - Install Frontend:
     ```bash
     helm install frontend -n schoolapp schoolapp/schoolapp-frontend
     ```
   - Install API:
     ```bash
     helm install api -n schoolapp schoolapp/schoolapp-api
     ```
   - Port Forwarding:
     ```bash
     kubectl -n schoolapp port-forward service/frontend 8001:8080
     kubectl -n schoolapp port-forward service/api 5000:5000
     ```

2. **ArgoCD Setup**
   - Install ArgoCD Server:
     ```bash
     kubectl create namespace argocd
     kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
     ```
   - Expose ArgoCD API Server:
     ```bash
     kubectl port-forward svc/argocd-server -n argocd 8002:443
     ```
   - Access ArgoCD:
     ```bash
     kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
     ```
     Visit [https://127.0.0.1:8002](https://127.0.0.1:8002) to log in.

3. **Delete & Re-create School App**
   - Delete School App:
     ```bash
     kubectl delete ns schoolapp
     ```
   - Apply School App in ArgoCD:
     ```bash
     kubectl apply -f argocdSchoolApp.yaml
     ```

4. **Testing and Logs**
   - Test School App:
     ```bash
     kubectl port-forward service/frontend 8001:8080 -n schoolapp
     kubectl port-forward service/api 5000:5000 -n schoolapp
     ```
   - Check API Logs:
     ```bash
     kubectl logs -n schoolapp -f $(kubectl get pods --template '{{range .items}}{{.metadata.name}}{{end}}' --selector=app=api) -c api
     ```

### Architecture Overview
- The repository is focused on deploying and managing components via Kubernetes and ArgoCD.
- Key components include MongoDB, frontend, and API services, which are built and deployed using Helm charts.
- ArgoCD is used for GitOps-based management.

