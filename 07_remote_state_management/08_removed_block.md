# Removed block

- If we want to stop managing a resource which was earlier being managed by terraform
- We can use removed block

## Earlier steps followed were
1. Remove the resource from config file
2. Execute terraform state rm command
This approach can lead to issues,
1. Manually the command has to be executed
2. THere is no track of command being executed

## 1. Comment the resource in config file
```
➜  import terraform state list
aws_s3_bucket.example
```

```
➜  import cat main.tf
/*
resource "aws_s3_bucket" "example" {
  bucket              = "terr-form-s3-state-store-1998"
  force_destroy       = false
  object_lock_enabled = false
  region              = "ap-south-2"
  tags                = {}
  tags_all            = {}
}
*/
```

## 2. Add the removed block
```
removed {
  from = aws_s3_bucket.example
  lifecycle {
    destroy = false
  }
}
```

```
➜  import terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:

Terraform will perform the following actions:

 # aws_s3_bucket.example will no longer be managed by Terraform, but will not be destroyed
 # (destroy = false is set in the configuration)
 . resource "aws_s3_bucket" "example" {
        id                          = "terr-form-s3-state-store-1998"
        tags                        = {}
        # (14 unchanged attributes hidden)

        # (3 unchanged blocks hidden)
    }

Plan: 0 to add, 0 to change, 0 to destroy.
╷
│ Warning: Some objects will no longer be managed by Terraform
│
│ If you apply this plan, Terraform will discard its tracking information for the following objects, but it will not delete them:
│  - aws_s3_bucket.example
│
│ After applying this plan, Terraform will no longer manage these objects. You will need to import them into Terraform to manage them again.
╵

```

```
$ terraform apply -auto-approve
$ terraform state list
```
