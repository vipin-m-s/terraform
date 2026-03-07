# s3 remote backend

## 1. Create s3 bucekt 

## 2. Create the below tf config
```
 $ ls -ltr
total 8
-rw-r--r--@ 1 vipin  staff  242  7 Mar 17:04 main.tf
 $ cat main.tf
terraform {
  backend "s3" {
    bucket       = "terr-form-s3-state-store-1998"
    region       = "ap-south-2"
    key          = "terraform.tfstate"
    use_lockfile = true

  }
}

resource "time_sleep" "test" {
  create_duration = "10s"
}
```

## 3.  Post apply, check s3 bucekt
```
 $ terraform apply -auto-approve

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # time_sleep.test will be created
  + resource "time_sleep" "test" {
      + create_duration = "10s"
      + id              = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.
time_sleep.test: Creating...
time_sleep.test: Still creating... [00m10s elapsed]
time_sleep.test: Creation complete after 10s [id=2026-03-07T11:36:23Z]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed
```

<img width="1363" height="473" alt="Screenshot 2026-03-07 at 5 06 55 PM" src="https://github.com/user-attachments/assets/0684933e-3b24-4480-af21-27cfacfb5d38" />


## 4. modify the main.tf to increase deplay to view state locking
```
terraform {
  backend "s3" {
    bucket       = "terr-form-s3-state-store-1998"
    region       = "ap-south-2"
    key          = "terraform.tfstate"
    use_lockfile = true

  }
}

resource "time_sleep" "test" {
  create_duration = "100s"
}
```
<img width="1375" height="560" alt="Screenshot 2026-03-07 at 5 08 25 PM" src="https://github.com/user-attachments/assets/55759cd4-1145-4890-97d4-b3620fe31651" />

