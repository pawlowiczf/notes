# Terraform, AWS EKS, ArgoCD

```sh
terraform apply -var-file="../global.tfvars"

# Zamiast trzymać stan, jako lokalny plik, warto przenieść go do bucketu S3
terraform init -migrate-state -backend-config="../state.config"
terraform init -backend-config="../state.config"
```

```sh
aws eks update-kubeconfig --name dev-ecmsp --region eu-central-1 --profile default
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
kubectl port-forward svc/argocd-server 8080:80 -n argocd
```