# Terraform, AWS EKS, ArgoCD

```sh
terraform apply -var-file="../global.tfvars"

# Zamiast trzymać stan, jako lokalny plik, warto przenieść go do bucketu S3
terraform init -migrate-state -backend-config="../state.config"

terraform init -backend-config="../state.config"
```