
# Infastructure as code
## What is it?
- It's a way of setting up your server infastructure using code instead of manual configuration.
- By using code you can automate the process.
- The nodes refer to the individual virtual machines that make up the infastructure.
-  Instead of manually setting up and configuring servers and networks, IaC lets developers define/manage their infrastructure using code.

## How is it usually used?
- Ansible, Chef, and Puppet are commonly used for configuration. 
- Azure Resource Manager, AWS and Google Cloud Deployment Manager can be used as tools within the cloud.
- Terraform and CloudFormation are commonly used for terraform.

## What are the benefits?
- Scalability: Infastructure as code gives the ability to provision and configure infrastructure automatically and at a desired scale, reducing the time required for manual provisioning.
- Version control: Infrastructure code can be stored in version control systems, allowing teams to track changes, collaborate, and use previous versions if needed.
- Easy to reproduce: Because we can use version control,infrastructure can be consistently reproduced across multiple environments, allowing for more consistency and reducing the time it takes.
- Fast change: Changes to the infrastructure can be made through one controller, allowing for fast development.
![](1.1.png)
## Ansible
### What is it?
Ansible is an open-source configuration management and automation tool that we can use for infastructure as code (IaC).

It provides a simple and efficient way to automate infrastructure management, making it easier to achieve consistency, scalability, and reproducibility across our network.
### why do we use it?
- It is simple to use, and doesn't require a lot of code.
- It can run thousands of servers at once.
- Agentless =  The virtual machines we access (aget nodes) don't need Ansible installed. Ansible only needs to be on the controller node. The Ansible Controller is where you are able to control everything.

# How to set up ping in ansible
We must first have vagrant open. In here, we add the vagrant file that we are using for Iac and `vagrant up`. After this, we `vagrant ssh controller, web and db` and do `sudo apt-get update` and `sudo apt-get upgrade` in each one. 
## Working in the terminal 
- We can first do `vagrant status`. We should see three VM running.
- We next do `vagrant ssh controller`
- To test that we are connected to the internet, we can do a `sudo apt-get update`.
- We can now enter the web VM with the command `ssh vagrant@192.168.33.10`. Note that this is the same result as doing `vagrant ssh web`.
- This will ask for a password when we do this.
- We can type `exit` and go into the DB VM with `ssh vagrant@192.168.33.11`.
- After we have been able to ssh in, we `exit` back into the controller.
- Here we enter the command `sudo apt install software-properties-common` to download common packages.
- We can now do the command `sudo apt-add-repository ppa:ansible/ansible`.
- To add, this we must then update. `sudo apt-get update -y`
- We now install ansible with `sudo apt install ansible -y`
- If we now do `sudo ansible --version`
- We can now cd into ansible with `cd /etc/ansible/`
- We now need to do `sudo nano hosts`
Under the section that says "# - A hostname/ip can be a member of multiple groups" with type below:

`[web]`

`192.168.33.10 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant`
- If we do the command `sudo ansible web -m ping` we should get the message.
![](1.2.png)
- Note: if this doesn't work, we can do `sudo nano ansible.cfg` and un-comment the `host_key_checking = False`. This however isn't usually recomended. 

## Adhoc commands
They allow you to run bash commands in ansible.


- Get OS of web host
`sudo ansible web -a "uname -a"`
- Get OS of db host
`sudo ansible db -a "uname -a"`
- Get date and time where the web host is
`sudo ansible web -a "date"`
- Get date and time where db host is
`sudo ansible db -a "date"`
- View the free and used memory of the web host
`sudo ansible web -a "free -m"`
- All files in theweb host
`sudo ansible web -a "ls -a"`


## Ansible playbook
Playbooks are written in YAML, and allow us to write a series of tasks tasks that we want our agent nodes to perform, allowing us to automate this.


- We create a playbook for the nginx:

`sudo nano nginx.yml`

In the playbook we then put:

```# add --- to start YAML file
---

# add the name of the host

- hosts: web

# Gain additional facts about the steps

  gather_facts: yes
# add admin access
  
  become: true

# install nginx

    tasks:
  
    - name: Installing Nginx 
      apt: pkg=nginx  state=present
```
## We run this playbook
- run playbook file with tasks written in yaml

`sudo ansible-playbook nginx.yml`

- check the status

`sudo ansible web -a "sudo systemctl status nginx"`

## Installing nodej
`sudo nano nodejs.yml`

```---
 hosts: web
  become: yes
  tasks:
     name: Add Node.js PPA
      shell: "curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -"
      args:
        warn: no

     name: Install Node.js
      apt:
        name: nodejs
        update_cache: yes
        state: present
     name: Install npm
     apt:
       name: npm
       state: present
```


## Playbook for copying over the app folder, create playbook file:


`sudo nano copy_app_over.yml`

## Add the tasks
```
---
- hosts: web

# gather additional facts about the steps
 
  gather_facts: yes
# give admin access to this file
  
  become: true

# task: get app folder on web agent
  
  tasks:
  
  - name: Copy App folder
     
    copy:
      src: /etc/app/app
      dest: /home/vagrant/
```

- We can then run:

`sudo ansible-playbook nodejs.yml`

## Copying the app folder to the web VM

`sudo ansible web -m copy -a "src=/etc/app/app dest=/home/vagrant"`

`sudo ansible web -a "ls"`

## Start the app up

```
sudo nano app.yml
---
- hosts: web
  gather_facts: yes
  become: true

  tasks:  
    - name: Install Node.js
      become_user: vagrant
      shell: curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -

    - name: Install nodejs package
      become_user: vagrant
      shell: sudo apt-get install -y nodejs

    - name: Install app dependencies
      become_user: vagrant
      shell: npm install
      args:
        chdir: /home/vagrant/app

    - name: Install PM2 globally
      shell: sudo npm install pm2 -g

    - name: Start the app with PM2
      shell: pm2 start app.js
      become_user: vagrant
      args:
        chdir: /home/vagrant/app
```
## We can now start the app
```
sudo ansible-playbook app.yml
```
![](1.5.png)