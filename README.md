# Infrastructure as Code(IaC)

## What is IaC ? 

IaC is a way of managing a computer infrastructure using code instead of manual processes. 
This means that instead of manually setting up servers, networks and other resources, you define them in code which can then be automatically deployed and configured. 

## Why do we use it ? 

- Automates infrastructure provisioning and configuration
- Ensures consistency accross different environments
- Enables scalability and flexibility
- Provides version control for infrastructure definitions
- Reduces chances of human error
- Infrastructure is more manageable and maintanable over time. 


## Configuration management

Configuration management is the process of managing the configuration of an organization's IT infrastructure. 

It involves tasks such as tracking changes, documenting items, and ensuring consistency across environments. It is important for reliability, security, and issue resolution.

## Orchestration

Orchestration automates and coordinates complex workflows across multiple systems or applications, making it easier to deploy and manage applications at scale.


## Tools used in IaC

- Terraform: A tool for declarative infrastructure provisioning across cloud providers and services.

- AWS CloudFormation: A tool for infrastructure provisioning using templates to define resources and dependencies in AWS.

- Ansible: A tool for automating infrastructure configuration management and deployment across systems and environments.

- Puppet: A tool for automating the configuration and management of infrastructure components across systems and environments.


## Who is using IaC ?

- Airbnb: Uses IaC to manage cloud infrastructure and automate resource provisioning.

- Netflix: Uses IaC to manage cloud infrastructure and automate application deployment.

- Capital One: Uses IaC to manage hybrid cloud environment and automate resource provisioning.

- GitHub: Uses IaC to manage cloud infrastructure and automate application deployment.

- NASA: Uses IaC to manage IT infrastructure and automate resource deployment and configuration.



# Terraform

Terraform is an open-source infrastructure as code software tool that enables you to safely create, change, and improve infrastructure and it is used primarily by DevOps teams to automate various infrastructure tasks.



# Ansible 

Ansible is a software tool that provides simple but powerful automation for cross-platform computer support.

Ansible doesn't depend on agent software and has no additional security infrastructure, so it's easy to deploy.


## Push configuration vs pull configuration

The main difference between the two methods is how the servers are configured. In the **pull method**, the to be configured server pulls its configuration from the controlling server whereas the **push method**, the controlling server pushes the configuration to the destination system.


---

# Guide


![](image.png)

- Create a `vagrantfile` in VS code and import the script to create 3 VMs using oracle.
---
The script below creates the "controller" VM with Ubuntu 18.04 OS, and hard-coded IP address.


![](cont.png)

---

This script creates the "web" VM with the same OS but different IP address.
![](web.png)

---

And lastly, this script creates the 3rd VM called "db". 


![](db.png)

---

Now we run `vagrant up` in the terminal.



This should start up 3 machines in your Virtual Box
- controller
- web
- db



SSH into all of them in separate GitBash terminals and run these commands to establish connection and run updates and upgrades:

- `sudo apt-get update -y`
- `sudo apt-get upgrade -y`


Next in the controller VM we prepare Ansible to install.
```
 `sudo apt-add-repository ppa:ansible/ansible`
 `sudo apt-get install software-properties-common` 
```

- Then we can run `sudo apt-get update -y` to apply updates. 

 `sudo apt-get install ansible -y` to install and check the version`ansible --version`


To be edited: 
 ```
vagrant@controller:~$ cd /etc
vagrant@controller:/etc$ cd ansible/
vagrant@controller:/etc/ansible$ pwd
/etc/ansible
vagrant@controller:/etc/ansible$ /etc/ansible
-bash: /etc/ansible: Is a directory
vagrant@controller:/etc/ansible$
```



