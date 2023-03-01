# Infrastructure as Code(IaC)

## What is IaC ? 

IaC is a way of managing a computer infrastructure using code instead of manual processes. 
This means that instead of manually setting up servers, networks and other resources, you define them in code which can then be automatically deployed and configured. 

## Why do we use it ? 

- Automates infrastructure provisioning and configuration
- Ensures consistency across different environments
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

- Chef: In IaC, Chef is typically used to define infrastructure and application configurations as code, which can be version-controlled and shared across teams. This allows more consistent and repeatable deployments and reduces the potential for human error. 

![](pictures/IaC.jpg)



---


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

**Agent software** typically refers to a piece of software that runs on a target system or device and is responsible for collecting and sending data back to a central management system. 

## Ansible VS Terraform

While both Ansible and Terraform are used for infrastructure automation,
Ansible is mainly focused on configuration management, while Terraform is primarily focused on infrastructure provisioning and management.

Depending on your specific needs, you may choose one tool over the other or use both in conjunction to achieve your automation goals. 


## Push configuration vs pull configuration

The main difference between the two methods is how the servers are configured. In the **pull method**, the to be configured server pulls its configuration from the controlling server whereas the **push method**, the controlling server pushes the configuration to the destination system.


---

# Ansible architecture guide step.1


![](pictures/image.png)

- Create a `vagrantfile` in VS code and import the script to create 3 VMs using oracle.

```
# -*- mode: ruby -*-
 # vi: set ft=ruby :
 
 # All Vagrant configuration is done below. The "2" in Vagrant.configure
 # configures the configuration version (we support older styles for
 # backwards compatibility). Please don't change it unless you know what
 
 # MULTI SERVER/VMs environment 
 #
 Vagrant.configure("2") do |config|
    # creating are Ansible controller
      config.vm.define "controller" do |controller|
        
       controller.vm.box = "bento/ubuntu-18.04"
       
       controller.vm.hostname = 'controller'
       
       controller.vm.network :private_network, ip: "192.168.33.12"
       
       # config.hostsupdater.aliases = ["development.controller"] 
       
      end 
    # creating first VM called web  
      config.vm.define "web" do |web|
        
        web.vm.box = "bento/ubuntu-18.04"
       # downloading ubuntu 18.04 image
    
        web.vm.hostname = 'web'
        # assigning host name to the VM
        
        web.vm.network :private_network, ip: "192.168.33.10"
        #   assigning private IP
        
        #config.hostsupdater.aliases = ["development.web"]
        # creating a link called development.web so we can access web page with this link instread of an IP   
            
      end
      
    # creating second VM called db
      config.vm.define "db" do |db|
        
        db.vm.box = "bento/ubuntu-18.04"
        
        db.vm.hostname = 'db'
        
        db.vm.network :private_network, ip: "192.168.33.11"
        
        #config.hostsupdater.aliases = ["development.db"]     
      end
    
    
    end
```


---
The script below creates the Ansible controller with Ubuntu 18.04 OS, and hard-coded IP address.



![](pictures/cont.png)


---


This script creates the "web" VM with the same OS but different IP address.


![](pictures/web.png)




---


And lastly, this script creates the 2nd VM called "db". 


![](pictures/db.png)


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


**NOTE**: This is how we get into the "hosts" file.  
 ```
vagrant@controller:~$ cd /etc
vagrant@controller:/etc$ cd ansible/
vagrant@controller:/etc/ansible$ pwd
/etc/ansible
```

---

**Ping**:



In order to be able to ping the web VM will need to change some configuration 



- `ls -a`
- `cd .ssh`
- `ls`
- `sudo nano authorized_keys`
Here we can see that we have the RSA key. 



Edit the hosts file using `cd /etc/ansible` in the controller and `sudo nano hosts` to be able to ping the "web" machine. We will need to edit the file.

 Dont forget to use the IP of web, as seen in the below code, specify the connection type->ssh, and the log in as well as the password. In this case it is `vagrant` for both for ease of use. 
```
[web]
192.168.33.10 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant
```


Now if we would like to ping the web machine we will need to change some configuration of web machine.
To connect using ssh from controller to web we use `ssh vagrant@192.168.33.10`(IP is of the web VM)
In the web:
- `cd /etc`
- `ls`
- `cd ssh`
- `sudo nano sshd_config`

We need to make sure these lines of code are uncommented:
- `PermitRootLogin prohibit-password`
- `PasswordAuthentication yes`
- `ChallengeResponseAuthentication no`

Now we will need to run `sudo systemctl restart ssh` for the changes to take effect.

Now we need to edit the `ansible.conf` file in the controller:
```
cd /etc/ansible
ls
sudo nano ansible.config
```
After [defaults] add this:

`host_key_checking = false`



Next in the controller we run `sudo ansible -m ping web` and the following should be the output if the previous configuration was successful:

```
192.168.33.10 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

---

 Useful `ad hoc` commands:

- `vagrant@controller:/etc/ansible$ sudo ansible web -a "uname -a"` gives info about the VM

- `sudo ansible all -a "date"` outputs time zone


- `sudo ansible all -a "free -m"` outputs free space available

- `sudo ansible all -a "ls -a"` to find hidden files

---

If we would like to copy a file from the Ansible controller onto the web VM here are the steps:

1. Create a file in the controller `sudo nano test.txt` and write some content into it

2. `sudo ansible web -m ansible.builtin.copy -a "src=test.txt dest=test.txt"`- This command specifies the source: "test.txt"m and the destination: "test.txt"

3. Next we can check in the web VM if the file has been copied over with the content. 


---

# Ansible architecture guide step.2

![](pictures/new_diagram.png)

As we can see on the diagram above, now we will need to install the required dependencies, packages and database into our VMs. 

- Step1.- Install required dependencies on web(Nginx, pm2, node js) using playbook and shell script.
- Step2.- Install mongoDB database on our db VM.
- Step3.- Establish the connection between web and db by creating an environment variable.


[Linux commands for installing dependencies](https://github.com/MarekMatyas/tech201_virtualization)
 

We also need to make sure we configure the `mongod.conf` file using `sudo nano /etc/mongod.conf` and set the bindIP to `0.0.0.0`. 

After that we will need to `restart` and `enable` mongod. 

---

**NOTE**: To achieve communication between our web and db VM's, we will need to create an environment variable on our web VM using `export DB_HOST=mongodb://192.168.33.11:27017/posts` with the IP of our db.

---


## Step 1.


- `sudo nano nginx-playbook.yml`
Create a playbook to configure Nginx web server in web machine

`---` to start a YAML file

```
# Create a playbook to configure Nginx web server in web machine

# Let's add --- 3 dashes to start a YAML file
---


# Where do we want this playbook to run
# Add the name of the host
- hosts: web
# Find the facts
  gather_facts: yes
# We need admin access
  become: true


# Now that we have access, we can start adding instructions to perform the task
# Install nginx in web machine
  tasks:
  - name: Install Nginx in web-server
    apt: pkg=nginx state=present
# Ensure that nginx is running - status is running
```


- `sudo ansible-playbook nginx-playbook.yml` to run the file 

- `sudo ansible web -a "systemctl status nginx"` to check the status of nginx

Check functionality insert the IP address of web VM. 

---

### Node, npm, pm2 

First we will need to copy our app script into the "web" machine. 

There are multiple ways of doing, and in this particular task we will be using the method of cloning from our repository.

We need to make sure we create our playbooks in the `/etc/ansible` directory.

First we need to create a directory in our web VM to store the cloning content. In this case we called it `task_repo`.

Then in our controller we can create a playbook called `clone_app`.

- `sudo nano clone_app-playbook.yml`

```
---
# Creating a playbook to clone GitHub repository where our our script for app is located

---
# What we would like to name this playbook
- name: Clone repository
# Where we want to clone this repo
  hosts: web
  tasks:
    - name: Clone repository
      git:
        repo: https://github.com/MarekMatyas/tech201_virtualization.git
        dest: /home/vagrant/task_repo

```

**NOTE**: 
- name of the task is "Clone repository"
- repo is the URL of the repository
- dest is the destination where want to clone the repo


Now if we would like to run this playbook we do this using `sudo ansible-playbook clone_app-playbook.yml`

---

Next we will need create another playbook called `pm2` using `sudo nano.

---

**Possible blocker at this stage**:


If we would like to install node and pm2, this may cause problem due to the pm2 may have issues with the version of node. We need to make sure that we install node version 10. and above as seen in the script below. 

---

The content for this playbook should be as follows:

```
# Playbook to configure npm, pm2, nodejs using shell script

---

# where do we want this playbook to run
# add the name of the host
- hosts: web
# Check the ping


# find the facts
  gather_facts: yes


# we need admin access
  become: true

# Now that we can the access, we can add the instructions to perform the task
  tasks:
  - name: Install NPM
    shell: |
      #!/bin/bash
      curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
      sudo apt-get install nodejs -y
      sudo npm install pm2 -g
```

**NOTE**- In this particular playbook we are using a shell script to install some dependencies such as node v12 and pm2. 

To indicate the shell script we use `|` symbol to specify that anything below that symbol is a shell script. 

Now to run this playbook we use the same command as before `sudo ansible-playbook pm2-playbook.yml`

--- 



Now we can move onto making a playbook called "run_app-playbook.yml" that will run the app for us.

To create a playbook we use `sudo nano run_app-playbook.yml`

We need to make sure that we use more accessible directory when writing the script for the playbook to be ran in the web VM. (home/task_repo/app). If we encounter a problem in this area, we can copy the entire app folder where we have our app script to more suitable location. 

```
# create a playbook to run app in web machine

# let`s add --- 3 dashes  to start a YAML file

---

# where do we want this playbook to run
# add the name of the host
- hosts: web
# this will check the ping


# find the facts
  gather_facts: yes
# yes can also be replaced with true


# we need admin access
  become: true

# we have the admin access, now we can add the instructions to perform the task
# getting the app running on web machine
  tasks:
  - name: Starting the app on web machine
    shell: |
      #!/bin/bash
      cd /home/task_repo/app
      npm install
      node app.js

```


Notice we are using shell script(#!/bin/bash) again to:
- Navigate into the correct directory `cd /home/task_repo/app`
- Install npm `npm install`
- To run the app `node app.js`

To run the this playbook we use `sudo ansible-playbook run_app-playbook.yml`

If all previous configuration has been success then this should be our output:

![](pictures/run_app.png)


Next we can check if the app is actually up and running by using the web VM's IP in the browser with the port 3000 because at this stage the reverse proxy has not been configured. 

![](pictures/app_displayed.png)