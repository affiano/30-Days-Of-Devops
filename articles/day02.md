# Configuring a EC2 instance at AWS using Terraform
Deploy a EC2 instance at AWS, using Terraform and infrastructure as a code principles.

## Solution
To solve this issue, we will use the [Terraform](https://www.terraform.io/) tool. Terraform is a tool that use the Infraestructure as a Code concepts to privision your cloud, infrastructure or service, using declarative configuration files.

The extension to terraform files is `.tf`

### Requirements
- A AWS account with programmatic access only (we will use the Access Key and Secret Key )
- A key pair created to access the EC2 instance

Our `main.tf` will be:

```
provider "aws"{
    region = var.aws_region" # will be defined in variables.tf
    
}

### Security Group
resource "aws_security_group" "access-extern" {
  name = "ssh-access"
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "ssh"
    cidr_blocks = ["0.0.0.0/0"]
  }

}


# EC2
resource "aws_instance" "ec2-terraform"{
    ami = var.ami_image  # will be defined in variables.tf
    key_name = var.key_name  # will be defined in variables.tf
    instance_type = var.instance_type  # will be defined in variables.tf
    vpc_security_group_ids = [aws_security_group.access-extern.id]
    associate_public_ip_address = "true"
    
    tags = {
        Name = "EC2 instance"
        Creator = "Terraform"
  }
}
```

The `variables.tf` file:

```
variable "aws_region" {
  description = "Default region to use"
  default     = "us-east-1"
}

variable "access_key" {
    description = "AWS access key"
    type = string
}

variable "secret_key" {
    description = "AWS access secret key"
    type = string
}

variable "instance_type" {
  description = "Instance type"
  default     = "t2.micro"
}

variable "ami_image"{
  description = "AMI default"
  default     = "ami-00eb20669e0990cb4"
}

variable "key_name" {
  description = "Name of the SSH keypair to use in AWS."
  default = "MyFavoriteKey"
}
```

The `outputs.tf` file:

```
output "public_ip-webserver" {
  value       = aws_instance.ec2-terraform.public_ip
  description = "Public IP address"
}
```

To plan this changes, we will run:

```
terraform plan
```

**Note**: At this time, we will be prompted to type the `key_access` and `secret_access`

And to apply this changes:

```
terraform apply
```

The result will show the IP address related to our instance.

In a few minutes we can test connecting by ssh.

### References
- https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html
- https://github.com/awsdocs/amazon-ec2-user-guide/blob/master/doc_source/ec2-key-pairs.md
- https://www.terraform.io/docs/providers/aws/r/instance.html
- https://www.terraform.io/docs/providers/aws/r/security_group.html

##
[<< Day 01. Deploying an Ruby App from Gitlab to Heroku ](day01.md)

[Day 03  >>](day03.md)
