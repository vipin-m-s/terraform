# import
- Resourcces created manually, without using terraform can be imported to terraform
- Using the terraform import block.

## 1. Create import block for resource
```
➜  import cat main.tf
import {
  id = "terr-form-s3-state-store-1998"
  to = aws_s3_bucket.example
}
```

## 2. terraform plan -generate-config-out=s3.tf
```
➜  import cat s3.tf
# __generated__ by Terraform
# Please review these resources and move them into your main configuration files.

# __generated__ by Terraform from "terr-form-s3-state-store-1998"
resource "aws_s3_bucket" "example" {
  bucket              = "terr-form-s3-state-store-1998"
  force_destroy       = false
  object_lock_enabled = false
  region              = "ap-south-2"
  tags                = {}
  tags_all            = {}
}
```

## 3. Now the tf config is genreated, but the resource is not present in state file. Perform apply to add to state file. 
```
terraform apply -auto-approve
```
```
➜  import cat terraform.tfstate
{
  "version": 4,
  "terraform_version": "1.14.6",
  "serial": 1,
  "lineage": "3b93a128-c78b-dd43-db0b-b83f5a337cd7",
  "outputs": {},
  "resources": [
    {
      "mode": "managed",
      "type": "aws_s3_bucket",
      "name": "example",
      "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
      "instances": [
        {
          "schema_version": 0,
          "attributes": {
            "acceleration_status": "",
            "acl": null,
            "arn": "arn:aws:s3:::terr-form-s3-state-store-1998",
            "bucket": "terr-form-s3-state-store-1998",
            "bucket_domain_name": "terr-form-s3-state-store-1998.s3.amazonaws.com",
            "bucket_prefix": "",
            "bucket_region": "ap-south-2",
            "bucket_regional_domain_name": "terr-form-s3-state-store-1998.s3.ap-south-2.amazonaws.com",
            "cors_rule": [],
            "force_destroy": false,
```
