# LEARN IKO

Terraform steps:
1. Scope - identify the infra for the project
2. Author - write configuration to define infra
3. Initialize - install required terraform providers
4. Plan - preview the changes
5. Apply - make the changes

Terraform Providers: https://registry.terraform.io/browse/providers

Sample Code:
```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.16"
    }
  }

  required_version = ">= 1.2.0"
}

provider "aws" {
  region  = "us-west-2"
}

resource "aws_instance" "app_server" {
  ami           = "ami-830c94e3"
  instance_type = "t2.micro"

  tags = {
    Name = "ExampleAppServerInstance"
  }
}

```

According this code Instance ID will be aws_instance.app_server
