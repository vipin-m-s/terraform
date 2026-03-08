# State locking

- When multiple users execute terraform apply at the same time
- It can create inconsistencies and corrupt the state file
- Locking should be inplace to avoid corrupted state file
- AT a time only one apply should be allowed

1) Terraform apply
2) Lock the state file
3) Perform changes to state file
4) Remove the lock
5) apply completes

State locking in local backend is done using lock file

1) terraform apply
2) Create a .terraform.tfstate.lock.info file
3) modify the state file
4) Remove the .terraform.tfstate.lock.info file
5) Apply completes

If any user tries to apply when the lock file is present. The apply fails with error message. 


```
 $ cat main.tf
resource "time_sleep" "test" {
	create_duration = "100s"
}
```
```
 $ terraform apply -auto-approve

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # time_sleep.test will be created
  + resource "time_sleep" "test" {
      + create_duration = "100s"
      + id              = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.
time_sleep.test: Creating...

```
When it is sleeping, .terraform.tfstate.lock.info file is created
```
➜  terraform ls -ltra
total 24
drwx------@ 113 vipin  staff  3616  7 Mar 16:51 ..
-rw-r--r--@   1 vipin  staff    59  7 Mar 16:51 main.tf
drwxr-xr-x@   3 vipin  staff    96  7 Mar 16:51 .terraform
-rw-r--r--@   1 vipin  staff  1153  7 Mar 16:51 .terraform.lock.hcl
-rw-r--r--@   1 vipin  staff     0  7 Mar 16:52 terraform.tfstate
drwxr-xr-x@   7 vipin  staff   224  7 Mar 16:52 .
-rw-------@   1 vipin  staff   212  7 Mar 16:52 .terraform.tfstate.lock.info
```

After 100 s completed sleeping, the .terraform.tfstate.lock.info should be removed
```
time_sleep.test: Still creating... [01m20s elapsed]
time_sleep.test: Still creating... [01m30s elapsed]
time_sleep.test: Creation complete after 1m40s [id=2026-03-07T11:23:52Z]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

```
➜  terraform ls -ltra
total 24
drwx------@ 113 vipin  staff  3616  7 Mar 16:51 ..
-rw-r--r--@   1 vipin  staff    59  7 Mar 16:51 main.tf
drwxr-xr-x@   3 vipin  staff    96  7 Mar 16:51 .terraform
-rw-r--r--@   1 vipin  staff  1153  7 Mar 16:51 .terraform.lock.hcl
-rw-r--r--@   1 vipin  staff   687  7 Mar 16:53 terraform.tfstate
drwxr-xr-x@   6 vipin  staff   192  7 Mar 16:53 .
```


# Locking errors

```
 $ terraform apply -auto-approve

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # time_sleep.test will be created
  + resource "time_sleep" "test" {
      + create_duration = "100s"
      + id              = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.
time_sleep.test: Creating...

```

applying when the lock is present on state file, gives below error
```
➜  terraform terraform apply -auto-approve
╷
│ Error: Error acquiring the state lock
│
│ Error message: resource temporarily unavailable
│ Lock Info:
│   ID:        4cdf55fe-386c-d3fb-e9c3-03be5311ea3d
│   Path:      terraform.tfstate
│   Operation: OperationTypeApply
│   Who:       vipin@Vipins-MacBook-Pro.local
│   Version:   1.14.6
│   Created:   2026-03-07 11:24:39.076093 +0000 UTC
│   Info:
│
│
│ Terraform acquires a state lock to protect the state from being written
│ by multiple users at the same time. Please resolve the issue above and try
│ again. For most commands, you can disable locking with the "-lock=false"
│ flag, but this is not recommended.
```
