# Jenkins-use-AWS
We take the Historical-document-layout-analysis as an example to demonstrate how to deploy this project to AWS through jenkins. We will create two EC2 instances, one for management and the other for running our project. The project is triggered by GitHub webhook. When the project is pushed any commit, Jenkins will automatically build the project.

## Requirement
In order to complete the deployment, you need a AWS account and create two EC2 instances, one is Jenkins manager and another is Jenkins slave. Our project will run in Jenkins slave. The minimum requirements for these two instances are as follows:
1. Jenkins manager can use any free instance. We use t2.micro instance with Amazon Linux platform.

2. Jenkins slave need at least 4GB of RAM and 12GB of storage. In this project, we use t2.medium and bind a 16GB volume, with Ubuntu 2020. We do not recommend using Amazon Linux or CentOS because there may be problems with docker adaptation.

## Security groups
For Jenkins manager and Jenkins slave, we configure two security groups respectively.
1. For Jenkins manager, we should add a Custom TCP rule in inbound rules, select port 8080, in the source, enter the IP address of the host (the local computer you are using to connect AWS). If you don't know the IP address, enter "what is my IP address" in any browser. This rule will open port 8080 for your host. Port 8080 is the default configuration page of Jenkins. 
In addition, you also need to add 3 similar rules to open 8080 for GitHub's webhook. In these 3 rules, change the source to the [IP address of GitHub hook](http://www.runoob.com/). 

Note: You can also set source to 0.0.0.0/0 here. But we do not recommend this because it will make your Jenkins manager open to everyone. 

2. For Jenkins slave we only need to add a single rule which is open port 5000. This is because our project needs to use this port. If you deploy another project, then this rule will be determined according to your project.

In addition, the security group has a default rule, which is to open port 22. This is the port we connect to AWS instances, so do not close this port.


