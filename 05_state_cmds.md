# state commands

We should never modify the state commands manually. It can lead to human errors.

## state list
To list all the resources in state file
```
➜  terraform terraform state list
aws_iam_user.dev
aws_security_group.sg
time_sleep.test
```

## state show RESOURCE
TO view the attribbutes of a resource, use show command
```
➜  terraform terraform state show aws_iam_user.dev
# aws_iam_user.dev:
resource "aws_iam_user" "dev" {
    arn                  = "arn:aws:iam::707264480025:user/dev"
    force_destroy        = false
    id                   = "dev"
    name                 = "dev"
    path                 = "/"
    permissions_boundary = null
    tags_all             = {}
    unique_id            = "AIDA2JLB7QMM4JLYL73PK"
}
```

## terraform state pull 
to fetch the current state file

## terraform state rm RESOURCE
- When we have created resource using terraform
- We have manually modified the resource so much that it cannot be managed via terraform
- We can choose to ignore an resource from terraform using rm command

Manually modify the security group
```
➜  terraform terraform plan
aws_iam_user.dev: Refreshing state... [id=dev]
aws_security_group.sg: Refreshing state... [id=sg-04c08b181b82a5c12]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # aws_security_group.sg will be updated in-place
  ~ resource "aws_security_group" "sg" {
        id                     = "sg-04c08b181b82a5c12"
        name                   = "prod-sg"
      ~ tags                   = {
          - "a" = "1" -> null
          - "b" = "2" -> null
          - "c" = "3" -> null
        }
      ~ tags_all               = {
          - "a" = "1" -> null
          - "b" = "2" -> null
          - "c" = "3" -> null
        }
        # (9 unchanged attributes hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.
```


If we remove the section from terraform config file, then the resource will be deleted.
We do not want resources to be deleted
```
➜  terraform cat main.tf
terraform {
  backend "s3" {
    bucket       = "terr-form-s3-state-store-1998"
    region       = "ap-south-2"
    key          = "terraform.tfstate"
    use_lockfile = true

  }
}

resource "aws_iam_user" "dev" {
        name = "dev"
}
```
```
  # aws_security_group.sg will be destroyed
  # (because aws_security_group.sg is not in configuration)
  - resource "aws_security_group" "sg" {
      - arn                    = "arn:aws:ec2:ap-south-2:707264480025:security-group/sg-04c08b181b82a5c12" -> null
      - description            = "Managed by Terraform" -> null
      - egress                 = [] -> null
      - id                     = "sg-04c08b181b82a5c12" -> null
      - ingress                = [
          - {
              - cidr_blocks      = [
                  - "0.0.0.0/0",
                ]
              - from_port        = 22
              - ipv6_cidr_blocks = []
              - prefix_list_ids  = []
              - protocol         = "tcp"
              - security_groups  = []
              - self             = false
              - to_port          = 22
                # (1 unchanged attribute hidden)
            },
        ] -> null
      - name                   = "prod-sg" -> null
      - owner_id               = "707264480025" -> null
      - region                 = "ap-south-2" -> null
      - revoke_rules_on_delete = false -> null
      - tags                   = {
          - "a" = "1"
          - "b" = "2"
          - "c" = "3"
        } -> null
      - tags_all               = {
          - "a" = "1"
          - "b" = "2"
          - "c" = "3"
        } -> null
      - vpc_id                 = "vpc-0a4fac716e6106f1d" -> null
        # (1 unchanged attribute hidden)
    }

Plan: 0 to add, 0 to change, 1 to destroy.
```


We can use terraform state rm 
```
➜  terraform terraform state list
aws_iam_user.dev
aws_security_group.sg
➜  terraform terraform state rm aws_security_group.sg
Removed aws_security_group.sg
Successfully removed 1 resource instance(s).
➜  terraform
➜  terraform terraform state list
aws_iam_user.dev
```

If the resource exists in config file and not in state file
- Terraform  will create the resource
```

➜  terraform cat main.tf
terraform {
  backend "s3" {
    bucket       = "terr-form-s3-state-store-1998"
    region       = "ap-south-2"
    key          = "terraform.tfstate"
    use_lockfile = true

  }
}

resource "aws_iam_user" "dev" {
        name = "dev"
}

resource "aws_security_group" "sg" {
  name = "prod-sg"
}

➜  terraform terraform plan
aws_iam_user.dev: Refreshing state... [id=dev]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_security_group.sg will be created
  + resource "aws_security_group" "sg" {
      + arn                    = (known after apply)
      + description            = "Managed by Terraform"
      + egress                 = (known after apply)
      + id                     = (known after apply)
      + ingress                = (known after apply)
      + name                   = "prod-sg"
      + name_prefix            = (known after apply)
      + owner_id               = (known after apply)
      + region                 = "ap-south-2"
      + revoke_rules_on_delete = false
      + tags_all               = (known after apply)
      + vpc_id                 = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

```

If the resource is removed from config and remove from state file
Then we will see no changes rewuired
- The resource will continue to exist in AWS console 
```
➜  terraform cat main.tf
terraform {
  backend "s3" {
    bucket       = "terr-form-s3-state-store-1998"
    region       = "ap-south-2"
    key          = "terraform.tfstate"
    use_lockfile = true

  }
}

resource "aws_iam_user" "dev" {
        name = "dev"
}
```
```
➜  terraform terraform plan
aws_iam_user.dev: Refreshing state... [id=dev]

No changes. Your infrastructure matches the configuration.

Terraform has compared your real infrastructure against your configuration and found no differences, so no changes are needed.
```
<img width="1139" height="591" alt="Screenshot 2026-03-07 at 6 35 36 PM" src="https://github.com/user-attachments/assets/8f32a04b-3961-4f94-8d42-c074d29a019f" />






## terraform state mv 
When we want to rename the local name of a resource
- we cannot directly rename in config file as it will delete the original resource and recreate a new resource
- We have to first move in state file
- If we move in state file and not in config file
- Then dev iam user will be created.
- Prod iam user will be deleted.
- We need to mv in state file and then move in config file

```
➜  terraform cat main.tf
terraform {
  backend "s3" {
    bucket       = "terr-form-s3-state-store-1998"
    region       = "ap-south-2"
    key          = "terraform.tfstate"
    use_lockfile = true

  }
}

resource "aws_iam_user" "dev" {
        name = "dev"
}
```

```
➜  terraform terraform plan
aws_iam_user.dev: Refreshing state... [id=dev]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create
  - destroy

Terraform will perform the following actions:

  # aws_iam_user.dev will be destroyed
  # (because aws_iam_user.dev is not in configuration)
  - resource "aws_iam_user" "dev" {
      - arn                  = "arn:aws:iam::707264480025:user/dev" -> null
      - force_destroy        = false -> null
      - id                   = "dev" -> null
      - name                 = "dev" -> null
      - path                 = "/" -> null
      - tags                 = {} -> null
      - tags_all             = {} -> null
      - unique_id            = "AIDA2JLB7QMM4JLYL73PK" -> null
        # (1 unchanged attribute hidden)
    }

  # aws_iam_user.prod will be created
  + resource "aws_iam_user" "prod" {
```


We do not want it to be destoryed and recreated
```
➜  terraform terraform state mv aws_iam_user.dev aws_iam_user.prod
terraform state list
Move "aws_iam_user.dev" to "aws_iam_user.prod"
Successfully moved 1 object(s).
➜  terraform terraform state list
aws_iam_user.prod
```

Now it is removed from state file
and present in config file

```
➜  terraform terraform plan
aws_iam_user.prod: Refreshing state... [id=dev]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create
  - destroy

Terraform will perform the following actions:

  # aws_iam_user.dev will be created
  + resource "aws_iam_user" "dev" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "dev"
      + path          = "/"
      + tags_all      = (known after apply)
      + unique_id     = (known after apply)
    }

  # aws_iam_user.prod will be destroyed
  # (because aws_iam_user.prod is not in configuration)
  - resource "aws_iam_user" "prod" {
      - arn                  = "arn:aws:iam::707264480025:user/dev" -> null
      - force_destroy        = false -> null
      - id                   = "dev" -> null
      - name                 = "dev" -> null
      - path                 = "/" -> null
      - tags                 = {} -> null
      - tags_all             = {} -> null
      - unique_id            = "AIDA2JLB7QMM4JLYL73PK" -> null
        # (1 unchanged attribute hidden)
    }

Plan: 1 to add, 0 to change, 1 to destroy.
```

Now both the places are updated
```
➜  terraform terraform plan
aws_iam_user.prod: Refreshing state... [id=dev]

No changes. Your infrastructure matches the configuration.

Terraform has compared your real infrastructure against your configuration and found no differences, so no changes are needed.
```

