# Jenkins-use-AWS
We take the Historical-document-layout-analysis as an example to demonstrate how to deploy this project to AWS through jenkins.

## Requirement
In order to complete the deployment, two EC2s are required, one as a jenkins manager to manage the project. The other is used as a worker to run our project.
1. Jenkins manager can use any free instance. We use t2.micro instance with Amazon Linux platform.
2. Workers need at least 4GB of RAM and 12GB of storage. In this project, we use t2.medium and bind a 16GB volume, with Ubuntu 2020.
