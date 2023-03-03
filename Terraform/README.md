# Terraform 

## How it works ?

Terraform in orchestration tool in IaC(Infrastructure as code) owned by HashiCorp.

It is tool for managing IaC. In simple terms, Terraform works by defining the desired state of your infrastructure in a configuration file, and then applying that configuration to you infrastructure. 


## Set-up Guide

First things first we will need to download and install terraform.

***NOTE**: Make sure you use appropriate version for your system when downloading terraform. In our case it would be `AMD64` for Windows. 

To download, navigate to this link -> [Terraform download](https://developer.hashicorp.com/terraform/downloads)

To install it we will need to extract the file that has been downloaded and move the `.exe` file to newly created folder called "Terraform" in our `C:/`

To install terraform, there is a quick and simple step guide -> [Terraform install](https://www.youtube.com/watch?v=SkcRSJWNRS8). **Be sure to use this guide to install terraform only!**

- After we installed terraform, we can open GitBash terminal as ADMIN and type in `terraform --version` to confirm the successful installation.

This should be your output:

```
Terraform v1.3.9
on windows_amd64
```

We will need to create a `main.tf` file in the location of our choice for example: `C:\Users\matya\IaC_terraform` and this is where we put our instructions such as: Lauching EC2, Security groups, VPC and ASG(Auto-Scaling Group).

This file will be used to help deploy this infrastructure and with help of Ansible we will be able to configure this infrastructure. 

**NOTE**: If we don't use AMI for this main.tf file, the EC2 instance that will be launched, will be empty. 


In terms of the Load Balancers, VPC's and other set up, that is where Terraform come in play. We will need to turn these configurations into code. 



- **Environment variables guide**:

To upload AWS credentials we need follow these steps:

- In your windows search bar, type in "Edit the system environment variables"

- Go to advanced section and click on `Environment variables`

- From there click on `New` in the "User variables" section.

- Lastly, name your key ID's appropriately "AWS_ACCESS_KEY_ID", "AWS_SECRET_KEY", import the key values and be sure to double check the values and press `OK`.

Then we can open VS code and open the folder we created before for this task.

- Location: `C:\Users\matya\IaC_terraform`

Our folder structures should be like this:

![](pictures/folders.png)

Our script for `main.tf` will be written in **HCL(HashiCorp Configuration Language)** that is used for defining infrastructure as code using Terraform.

```

# launch ec2
# which cloud provider tp specify

provider "aws" {
    region = "eu-west-1"

}

resource "aws_instance" "app_instance" {
    ami = "ami-029d17cd5c8c4b76e"
    instance_type = "t2.micro" 
    associate_public_ip_address = true
    tags = {
        Name = "tech201-marek-terraform-app"
    }
}
```

Let's break this code down:

---
- This block declares the provider, which in this case is AWS. It also specifies the region where the resources will be created. 

```
provider "aws" {
    region = "eu-west-1"
}
```
---
This block defines a resource of type "aws_instance" and gives it a unique identifier "app_instance". It specifies the AMI to use for the instance, the instance type, and enables the instance to have a public IP address. It also assigns a tag to the instance, which can be used for identification and organization purposes.

```
resource "aws_instance" "app_instance" {
    ami = "ami-029d17cd5c8c4b76e"
    instance_type = "t2.micro" 
    associate_public_ip_address = true
    tags = {
        Name = "tech201-marek-terraform-app"
    }
}

```
---


**NOTE**: If we try to run `terraform init` command which serves to perform the task provided, in the VS code and it is isn't working, we might have to open GitBash terminal as ADMIN and navigate into the correct directory we can attempt to run `terraform init` again. 

If successful, this should be our output: 

![](pictures/init.png)

Next we can run `terraform plan` command that is used to preview the changes that will be made to our infrastructure before actually applying those changes. 

If successful, this should be our output:

![](pictures/plan.png)


Lastly, we can ran the final command `terraform apply` to aplly the changes defined in our Terraform configuration to our infrastructure and this will actually launch the instance. 

If we receive this output, we can go to AWS console and check if the instance has been launched and it is running correctly...

![](pictures/apply.png)

![](pictures/instance.png)

--- 

If we wish to destroy the instance from our terminal, we can run `terraform destroy` and it will terminate our instance.

![](pictures/destroy.png)



