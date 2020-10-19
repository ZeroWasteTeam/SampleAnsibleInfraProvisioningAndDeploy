# SampleAnsibleInfraProvisioningAndDeploy
We will show how to provision Infrastructure in Azure and deploy a container with Ansible playbooks


In this article we will use Ansible (Infrastructure as Code) to swiftly bring up a Microsoft Azure Virtual Machine instance in East US on a static IP, add a DNS Zone for the site in mention and install docker/docker-compose on it.

We will use "ansiblecontroller" as an example in this repo. Also you should also a **Azure Account**.

We will use the Azure **Cloud shell** to deploy our ansible playbook.

Before we start creating the VM with Ansible we would require the public key created for authorization.

Generate an SSH Key Pair (no password) and restrict permissions on it, run the below command on Cloud Shell:
```
$ ssh-keygen -q -t rsa -b 2048 -N '' -f ~/.ssh/ansiblecontroller && chmod 400 ~/.ssh/ansiblecontroller
```
Then we need to run the Ansible playbook (attached in the repo)
```
ansible-playbook azure_create_vm.yml
```
Kindly note the public IP address which will be the output of the above script **"The public IP is <ip_address>."**

Once the resources are created on Aruze, connect to the VM using the below command through the Cloud Shell

```
ssh -i ~/.ssh/ansiblecontroller.pub azureuser@<ip_address>
```
Type yes and hit enter to accept.

Node which we just created we will call it **Server**. 

In order to demonstrate the reals essence of Ansible( Deployment) we need atleast one more node to run our experiments

So please go ahead and rerun the same **azure_create_vm.yml** playbook, ofcourse you need to update the entries like VM name etc. Ensure to copy the IP address of the same.


On the Controller:
To update and upgrade, log into the server to host Ansible and issue the following commands:
```
sudo apt-get update
sudo apt-get upgrade -y
```
Once that process completes, reboot the server (if necessary). You're now ready to install.

## Installing Ansible
Next, install Ansible. Here are the steps to make that happen:

1. Log into the Ubuntu Server that will host Ansible
2. Install the necessary repository with the command sudo apt-add-repository ppa:ansible/ansible.
3. Update apt with the command sudo apt-get update.
4. Install Ansible with the command sudo apt-get install ansible -y.
5. Because Ansible requires a Python interpreter (in order to run its modules), we need to install Python as well. For that, issue the command:

```
sudo apt-get install python -y
```
**Note**: You may find Python already installed.

At this point, Ansible is installed and ready to go.

## Configure SSH access to the server
Next, we need to make it possible for our node to access the Ansible server. We do this via Secure Shell (SSH). Copy the server's SSH public key to the node. If your server doesn't have a key yet, generate one with the command:
```
ssh-keygen
```
Display the contents of the public SSH key with the command:

cat ~/.ssh/id_rsa.pub
Here's what you do with the output of that command:

1. Copy the text from the key.
2. Log into your node server.
3. Issue the command sudo -s.
4. Open the authorized_keys file with the command sudo nano ~/.ssh/authorized_keys.
5. Paste the contents of the server key at the bottom of this file.
6. Save and close the file.
If you want to simplify this process, issue the command (from the Ansible server):
```
ssh-copy-id NODE_IP
```
Where NODE_IP is the IP Address of the node to be added.

To test the newly added key, go back to your Ansible server and SSH to the node machine. Complete this for all of the nodes you want connected to Ansible.


## Setting up our node
Next, make sure Ansible knows the location of our node. Issue the command:
```
sudo nano /etc/ansible/hosts
```
In that file, create a new group for your nodes (in our case, we've only connected one node) and associate the IP addresses like so:

[group_name]
ALIAS NODE_IP
Where group_name is the name of the group to be created, ALIAS is an alias for the node, and NODE_IP is the IP address of your node. If you have more than one node, list them like so:

[webservers]
WEB1 192.168.1.100
WEB2 192.168.1.101
WEB3 192.168.1.102
Save and close that file. You can now test this by pinging all of your added nodes with the command:
```
ansible -m ping all
```

Once the above steps are completed you are all set to install docker and run application on your node using Ansible.

## Install Docker
```
sudo apt-get install docker.io python3-docker -y
```

Once the installation is complete, start and enable the docker service with the commands:
```
sudo systemctl start docker
sudo systemctl enable docker
```
Finally, add your user to the docker group with the command:
```
sudo usermod -aG docker $USER
```
Log out and log back in so the changes will take effect.

## How to create the directory structure and files
On the Ansible server, create a new directory with the following command:
```
mkdir docker_project
```
Change into the docker_project directory with the command cd docker_project and create a hosts file with the command:
```
nano hosts
```
In that file, add the following contents:
```
[webserver]
SERVER_IP

[webserver:vars]
ansible_python_interpreter=/usr/bin/python3
```
Where SERVER_IP is the IP address of the other server(s). Save and close the file.

We're going to create a playbook that will do the following:

1. Install aptitude
2. Install a number of dependencies
3. Add a docker repository
4. Install docker-ce
5. Install the docker Python module
6. Pull the official Ubuntu image
7. Create four containers based on the Ubuntu image 

Issue the command nano ubuntu_playbook.yml.
And that's it. You now know how to deploy a Docker container by way of an Ansible playbook.
