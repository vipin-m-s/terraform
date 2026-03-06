# Base module structure

We need to create modules folder in the root directory 
ANd within the modules folder we should create folder for each module such as ec2, s3, sg etc

Each team will then refer these modules as per their requirement
```
├── main.tf
├── modules
│   ├── ec2
│   └── sg
├── teams
│   ├── A
│   └── B
```


# Creating a module 
- We  just have to place the regular resource block inside the modules folders.
- There is no other changes required
- Public modules might have all options of ec2_instancce but we can create modules with options as per the use case

$ vi modules/ec2/main.tf
```
resource "aws_instance" "webapp" {
    ami = "ami-090b9c8aa1c84aefc"
    instance_type = "t3.micro"
}
```
