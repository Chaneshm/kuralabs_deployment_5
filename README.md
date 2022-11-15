## Deployment 5


## Intro
Welcome to Deployment 5. In this deployment we will be deploying a container to Amazon ECS. This will be done using our Jenkins server to command two amazon EC2 which serve as agents. One agent will be the Terraform server and the other will be our Docker Server. We will first be creating our image within the Docker Server and then pushing the image to dockerhub. We will then tell our terraform agent to pull that image down and deploy it to ECS.

DEPLOYMENT DOCUMENTATION CAN BE FOUND HERE: https://github.com/Chaneshm/kuralabs_deployment_5/blob/main/Dep5Documentation.pdf

## Jenkins
This deployment assumes you have an active Jenkins server already running. If not, please defer to userdata script: https://github.com/Chaneshm/kuralabs_deployment_5/blob/main/userdatascripts/jenkinsuserdata.sh


## Terraform
Our next server we must have running is Terraform. This will be used for deploying our image to Amazon ECS. Below I have provided a userdata script for launching this server within an Amazon EC2. This script will also be located within this github repo HERE: https://github.com/Chaneshm/kuralabs_deployment_5/blob/main/userdatascripts/terraformuserdata.sh


## Docker
Our next step is to install docker onto another EC2 as we will need to build the image using a dockerfile. This will then be pushed to Dockerhub so that our terraform server can pull and deploy it to ECS. Userdata script can be found HERE: https://github.com/Chaneshm/kuralabs_deployment_5/blob/main/userdatascripts/dockerUserData.sh 
