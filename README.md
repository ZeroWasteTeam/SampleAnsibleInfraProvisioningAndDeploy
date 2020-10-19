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


