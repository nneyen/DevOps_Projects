
# Project 1 : Set up a Multi-tier Web App Stack Locally

In this project, we will learn to set up a multi-tier Web App Locally.  This project is originally created by Imran Teli and can be found [here](https://www.udemy.com/course/devopsprojects/?src=sac&kw=devops+projects)

## Prerequisites
Before beginning, Install the following

* Hypervisor - Oracle VM Box
* Vagrant
* CLI - We will use git
* An IDE of your choice, I use VSCode

Next clone the git repository

```
https://github.com/nneyen/vprofile-project.git

```


## Architecture
This first architecture (redrawn from original) shows the general overview of the project. 
![Architecture](images/p1-architecture.png)

## Step 1 : Setup VM

In this step we are going to bring up all VMs using the ` vagrant up ` command. 

*Vagrant allows to create and configure virtual environments, which are essentially isolated software systems running on your computer.* 

- first we will go to the directory where the vagrant file exists the file name is vagrantFile

- next install the vagrant plug in using the command 
```
vagrant plugin install vagrant-hostmanager
```
- then you can run the command to bring up the VMs
```
vagrant up

```
After a few minutes, you can check to see if your VMs are running. This usually takes a while so you have to be patient. It should look like this when it up and running
![Vagrant Up Result](images/vm-running.png)



* Next we will verify that our VMS by pinging each one. To do that, we will ssh into each VM using this command `vagrant ssh <vm_name>`. The VM name is in the VagrantFile

*Because of the plug in we installed earlier, all the vm hostnames and `/etc/hosts` will be automaticall updated. To confirm run the command `cat /etc/hosts`*

```
vagrant ssh web01
```
* ping app01 from web01 using the command `ping app01`. Remember that according to the diagram, Web tier communicates with the app tier
![ping app01](images/ping_app01.png)

* now we have confirmed that they are connected, we can logout using the `logout` command

* Repeat this for the app01 vm, first ssh into `app01`, and then check its connectivity to the backend servers `rmq01`, `db01` and `mc01`. Remember to logout once you have confirmed connectivity. 

## Step 2: Provisioning Services
![Web Architecture](images/vprofile-web-architecture.png)

* We will now provision 6 services in total and in the order mentioned below
|Service|Description|
|-------|-----------|
|MySQL | Database Service|
|Memcache| DB Caching Service|
|Rabbit MQ| Messaging Brokering Agent|
|Tomcat| Application Service|
|Nginx| Web Service |





