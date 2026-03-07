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
2) Create a terraform.lock.info file
3) modify the state file
4) Remove the terraform.lock.info file
5) Apply completes

If any user tries to apply when the lock file is present. The apply fails with error message. 
