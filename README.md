# Jenkins-use-AWS
We take the Historical-document-layout-analysis as an example to demonstrate how to deploy this project to AWS through jenkins. We will create two EC2 instances, one for management and the other for running our project. The project is triggered by GitHub webhook. When the project is pushed any commit, Jenkins will automatically build the project.

## Requirement
In order to complete the deployment, you need some basic knowledge about AWS and an AWS account. We will create two EC2 instances, one is Jenkins manager and another is Jenkins slave. Our project will run in Jenkins slave. The minimum requirements for these two instances are as follows:
1. Jenkins manager can use any free instance. We use t2.micro instance with Amazon Linux 2 AMI (HVM) (the default choice when you create EC2).

2. Jenkins slave need at least 4GB of RAM and 12GB of storage. In this project, we use t2.medium and bind a 16GB volume, with Ubuntu 20.04. We do not recommend using Amazon Linux or CentOS because there may be problems with docker adaptation.

## Security groups
For Jenkins manager and Jenkins slave, we configure two security groups respectively.
1. For Jenkins manager, we should add a Custom TCP rule in inbound rules, select port 8080, in the source, enter the IP address of the host (the local computer you are using to connect AWS). If you don't know the IP address, enter "what is my IP address" in any browser. This rule will open port 8080 for your host. Port 8080 is the default configuration page of Jenkins. 
In addition, you also need to add 3 similar rules to open 8080 for GitHub's webhook. In these 3 rules, change the source to the [IP address of GitHub hook](http://www.runoob.com/). 

<b>Note</b>: You can also set source to 0.0.0.0/0 here. But we do not recommend this because it will make your Jenkins manager open to everyone. 

2. For Jenkins slave we only need to add a single rule which is open port 5000. This is because our project needs to use this port. If you deploy another project, then this rule will be determined according to your project. Set source to 0.0.0.0/0, than anyone can visit this project.

In addition, the security group has a default rule, which is to open port 22. This is the port we connect to AWS instances, so do not close this port.

## Configure Jenkins manager
In AWS EC2 console, select jenkins manager instance. In the upper right corner click "connect" you will find several ways to connect instance. After connecting to this instance, we need to install jenkins on this instance. jenkins is based on Java, so we should install Java first.   

`	sudo yum install java -y`  

Now download jenkins repo:  

`sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo`  

`sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key`  

You can choose the corresponding [Jenkins version](https://pkg.jenkins.io/) according to the OS. We use the redhat-stable jenkins repo for our system, if you using the Amazon Linux you can use this jenkins version as well.
Install jenkins and start the service:  

`sudo yum install jenkins`  

`sudo service jenkins start`  

Use `systemctl status jenkins` to check the status of jenkins. If jenkins is running, open the browser and enter: <Public IPv4 address for jenkins manager instance>8080 to enter the jenkins configuration page. The Public IPv4 address you can find in AWS EC2 console. Now you enter the jenkins configuration webpage, you need to enter the password to unlock it. You can use `sudo cat ...` command to get the password. Here we install the default plugin which will contain the git plugins we need. 

## Configure jenkins slave
We use Ubuntu 20.04 instead of the same operating system as jenkins manager (Amazon Linux). Because Amazon Linux has encountered some problems in docker, you should choose OS system based on your project.  
This instance is used to deploy our project, so there is no need to install jenkins. But in order to manage use jenkins manager, we need to install Java.   

`sudo apt-get install openjdk-11-jdk`  
The Java version should be the same as the jenkins manager.   

After that, [install docker](https://docs.docker.com/engine/install/ubuntu/) and [docker-compose](https://docs.docker.com/compose/install/).    
Now, we done everything in this instance.

## Add jenkins slave in jenkins manager
