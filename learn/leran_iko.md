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

We can now issue the command to deploy this instance:
terraform init
terraform plan
terraform apply

aws_instance.app_server: Creating...
aws_instance.app_server: Still creating... [10s elapsed]
aws_instance.app_server: Still creating... [20s elapsed]
aws_instance.app_server: Still creating... [31s elapsed]
aws_instance.app_server: Creation complete after 35s [id=i-02cc6869b68eabfda]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

aws ec2 describe-instances --instance-ids i-02cc6869b68eabfda --region us-west-2

Now we would like to modify and we will change ami what will mean that we have Ubuntu as deployment.

Outout will be:

aws_instance.app_server: Destroying... [id=i-02cc6869b68eabfda]
aws_instance.app_server: Still destroying... [id=i-02cc6869b68eabfda, 10s elapsed]
aws_instance.app_server: Still destroying... [id=i-02cc6869b68eabfda, 20s elapsed]
aws_instance.app_server: Still destroying... [id=i-02cc6869b68eabfda, 30s elapsed]
aws_instance.app_server: Destruction complete after 31s
aws_instance.app_server: Creating...
aws_instance.app_server: Still creating... [10s elapsed]
aws_instance.app_server: Still creating... [20s elapsed]
aws_instance.app_server: Still creating... [30s elapsed]
aws_instance.app_server: Still creating... [40s elapsed]
aws_instance.app_server: Still creating... [50s elapsed]
aws_instance.app_server: Creation complete after 55s [id=i-00bd493c29b948e49]

Apply complete! Resources: 1 added, 0 changed, 1 destroyed.

we can check the job with the aws command
aws ec2 describe-instances --instance-ids i-00bd493c29b948e49 --region us-west-2

usefull command here is:
terraform show

To destroy the instance we will need to destory the instance. Command we will use:
terraform destroy



aws_instance.app_server: Destroying... [id=i-00bd493c29b948e49]
aws_instance.app_server: Still destroying... [id=i-00bd493c29b948e49, 10s elapsed]
aws_instance.app_server: Still destroying... [id=i-00bd493c29b948e49, 20s elapsed]
aws_instance.app_server: Still destroying... [id=i-00bd493c29b948e49, 30s elapsed]
aws_instance.app_server: Destruction complete after 31s

Destroy complete! Resources: 1 destroyed.

## DEFINE INPUT VARIABLES

To define variables we need to create file named variables.tf
```
variable "instance_name" {
  description = "Value of the Name tag for the EC2 instance"
  type        = string
  default     = "ExampleAppServerInstance"
}
```

now we will reconfigure our main.tf to use this variable. New config will do the same as previous but we will read the name from variables.tf file.

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
#  ami           = "ami-830c94e3"
  ami           = "ami-08d70e59c07c61a3a"
  instance_type = "t2.micro"

  tags = {
    Name = var.instance_name
  }
}

```

aws_instance.app_server: Creating...
aws_instance.app_server: Still creating... [10s elapsed]
aws_instance.app_server: Still creating... [20s elapsed]
aws_instance.app_server: Still creating... [30s elapsed]
aws_instance.app_server: Still creating... [40s elapsed]
aws_instance.app_server: Still creating... [50s elapsed]
aws_instance.app_server: Still creating... [1m0s elapsed]
aws_instance.app_server: Creation complete after 1m5s [id=i-05af62b211a08bf7c]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

We can update the name by changing in variables.tf and execute apply again
aws_instance.app_server: Refreshing state... [id=i-05af62b211a08bf7c]

Terraform used the selected providers to generate the following execution
plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # aws_instance.app_server will be updated in-place
  ~ resource "aws_instance" "app_server" {
        id                                   = "i-05af62b211a08bf7c"
      ~ tags                                 = {
          ~ "Name" = "ExampleAppServerInstance" -> "ExampleAppServerInstanceIko"
        }
      ~ tags_all                             = {
          ~ "Name" = "ExampleAppServerInstance" -> "ExampleAppServerInstanceIko"
        }
        # (29 unchanged attributes hidden)

        # (7 unchanged blocks hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.


or we can use this command to make the change

$ terraform.exe apply -var "instance_name=MojaMasina"
aws_instance.app_server: Refreshing state... [id=i-05af62b211a08bf7c]

Terraform used the selected providers to generate the following execution
plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # aws_instance.app_server will be updated in-place
  ~ resource "aws_instance" "app_server" {
        id                                   = "i-05af62b211a08bf7c"
      ~ tags                                 = {
          ~ "Name" = "ExampleAppServerInstanceIko" -> "MojaMasina"
        }
      ~ tags_all                             = {
          ~ "Name" = "ExampleAppServerInstanceIko" -> "MojaMasina"
        }
        # (29 unchanged attributes hidden)

        # (7 unchanged blocks hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.app_server: Modifying... [id=i-05af62b211a08bf7c]
aws_instance.app_server: Modifications complete after 3s [id=i-05af62b211a08bf7c]

Apply complete! Resources: 0 added, 1 changed, 0 destroyed.

This with the command is not OK because changes wont be saved and if we do terraform plan or apply we will see that we will be return to previous. So not OK. Hot o fix this.

## Outputs 

We need to create outputs.tf

we will add the following

```
output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.app_server.id
}

output "instance_public_ip" {
  description = "Public IP address of the EC2 instance"
  value       = aws_instance.app_server.public_ip
}

```

On end this will add additional lines in the output

Apply complete! Resources: 0 added, 1 changed, 0 destroyed.

Outputs:

instance_id = "i-05af62b211a08bf7c"
instance_public_ip = "54.201.85.96"


now we can serch by this attributs like this:

$ terraform.exe output
instance_id = "i-05af62b211a08bf7c"
instance_public_ip = "54.201.85.96"

or more specific

$ terraform.exe output instance_id
"i-05af62b211a08bf7c


Next Steps:
Next Steps
This concludes the getting started tutorials for Terraform. Now you can use Terraform to create and manage your infrastructure.

For more hands-on experience with the Terraform configuration language, resource provisioning, or importing existing infrastructure, review the tutorials below.

Configuration Language - Get more familiar with variables, outputs, dependencies, meta-arguments, and other language features to write more sophisticated Terraform configurations.

Modules - Organize and re-use Terraform configuration with modules.

Provision - Use Packer or Cloud-init to automatically provision SSH keys and a web server onto a Linux VM created by Terraform in AWS.

Import - Import existing infrastructure into Terraform.
