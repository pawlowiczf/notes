# Terraform, AWS EKS, ArgoCD

```sh
terraform apply -var-file="../global.tfvars"

terraform init -migrate-state -backend-config="../state.config"

terraform init -backend-config="../state.config"
```