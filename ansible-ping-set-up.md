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

[web]

192.168.33.10 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant
- If we do the command `sudo ansible web -m ping` we should get the message.
![](1.2.png)