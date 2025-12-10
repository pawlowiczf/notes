```sh
aws configure --profile terraform-pc
aws sts get-caller-identity --profile terraform-pc
```

```sh
aws sts assume-role --role-arn <role-arn> --role-session-name terraform --profile <profile>
```

