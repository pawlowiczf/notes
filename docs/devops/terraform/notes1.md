```sh
aws configure --profile TerraformAgent
aws sts get-caller-identity --profile TerraformAgent
```

```sh
aws sts assume-role --role-arn <role-arn> --role-session-name terraform --profile <profile>
```

