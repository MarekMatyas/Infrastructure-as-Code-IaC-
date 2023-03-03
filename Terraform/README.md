# Terraform 

## How it works ?

Terraform in orchestration tool in IaC(Infrastructure as code) owned by HashiCorp.

It is tool for managing IaC. In simple terms, Terraform works by defining the desired state of your infrastructure in a configuration file, and then applying that configuration to you infrastructure. 


## Set-up Guide

First things first we will need to download and install terraform.

***NOTE**: Make sure you use appropriate version for your system when downloading terraform. In our case it would be `AMD64` for Windows. 

To download, navigate to this link -> [Terraform download](https://developer.hashicorp.com/terraform/downloads)

To install it we will need to extract the file that has been downloaded and move the `.exe` file to newly created folder called "Terraform" in our `C:/`

To install terraform, there is a quick and simple step guide -> [Terraform install](https://www.youtube.com/watch?v=SkcRSJWNRS8)

- After we installed terraform, we can open GitBash terminal as ADMIN and type in `terraform --version` to confirm the successful installation.

This should be your output:

```
Terraform v1.3.9
on windows_amd64
```

We will need to create a `main.tf` file where we put our instructions such as: Lauching EC2, Security groups, VPC and ASG(Auto-Scaling Group).

This file will be used to help deploy this infrastructure and with help of Ansible we will be able to configure this infrastructure. 

**NOTE**: If we don't use AMI for this main.tf file, the EC2 instance that will be launched, will be empty. 


So far we launched an EC2 instance using Ansible instead of using AWS console and launching it manually.

In terms of the Load Balancers, VPC's and other set up, that is where Terraform come in play. We will need to turn these configurations into code. 



- **Environment variabels guide**:

To upload AWS credentials we need follow these steps:

- In you search bar, type in "Edit the environment system variables"

- Go to advanced section and click on `Environment variables`

- From there click on `New` in the "User variables" section.

- Lastly, name your key ID's appropriately "AWS_ACCESS_KEY_ID", "AWS_SECRET_KEY", import the key values and be sure to double check the values and press `OK`.

After that we can open a new GitBash terminal as ADMIN, navigate