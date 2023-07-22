# Jenkins-CICD-pipeline-on-AWS
Implementing a CICD pipeline using Jenkins, Docker, Sonarcube, Github webhook on AWS infrastructure

In this tutorial, we'll use Jenkins, SonarQube, Docker, and AWS to build an automated CI/CD pipeline for your web application. Every time you push new code to your GitHub repository, this pipeline will automatically build, test, analyze, and deploy your project to your AWS EC2 instance.


Prerequisite:

Account on GitHub

EC2 Instances knowledge and an AWS account

Understanding of SonarQube, Docker, and Jenkins

Steps to cover:
Create three EC2 instances with a security group that allows all traffic from the internet.

Install Jenkins, SonarQube, and Docker to each EC2 instance respectively.

Connect Jenkins instance to SonarQube, Docker instances, and to itself also through SSH.

Make those SSH connections password-less generating an ssh-key and saving their IDs.

Install plugin - SSH2 Easy on Jenkins and set server.

Set server groups and server sites of Jenkins, SonarQube, and Docker.

Create a new job at Jenkins and add a git link for your repository with the branch you want to build and deploy.

Add build steps in configuring the pipeline to copy code from Jenkins workspace to SonarQube and Docker instance for analysis and deployment.

On Jenkins VM

sudo apt update

sudo apt install Openjdk11 

Install Jenkins

Allow port 8080 inbound rule on sg

systemctl status  Jenkins

Copy the secret key and public ip of the instance

sudo cat /var/lib/jenkins/secrets/initialAdminPassword and generate the password

Login to Jenkins webpage using the public ip and 8080 and use the secret key as the admin password.

Install suggested pluggins

Create a user in Jenkins, add all parameters, save and start using Jenkins

Create item, give it name automated -pipeline, freestyle project. Under source code management Click on git and paste the repo url. Add branch and click on save


Configure - Build Trigger click Github hook trigger for GITScm polling

Go to GitHub repo and click on settings - web hooks and add web hook. Copy the Jenkins url/github-webhook/ and paste on payload URL. Click on let me select individual events and allow pull, push request and click on add web hook

Then build a pipeline and then make a change on the repo and validate. Workspace on Jenkins shows all folders from GitHub

SSH to sonarqube VM and change hostnamectl set-hostname sonar cube

/bin/bash

sudo apt update

sudo apt install openjdk-17-jre

Go to sonarqube website and download community edition. Copy the link and paste with wget command Unzip the file, install sudo apt unzip

Unzip filename

cd sonarqube folder

cd bin

cd linux

./sonar.sh

./sonar.sh - console to see the logs

Add port 9000 to the sg for inbound on sonarqube VM

Copy public of sonarcuube and port to login in with admin/admin. Replace the password and update

Click on manually

Give project key and project display name, main branch and set up

Click on Jenkins, GitHub click continue, click continue Under create a Jenkins file, click others

Copy the following codes

Sonar-project.properties and Jenkinsfile , copy the two codes

Click on finish tutorial

Under security create a token, generate token, type; pick "Global analysis token" and pick Expire 30days. Copy the token for keeps

On Jenkins, managed Jenkins, available pluggins and search for sonarqube scanner. Install without restart

Also install ssh2 easy

Go to global tool configuration and you will see sonarqube scanner. Click on add, give it name, install automatically and save it Go to configure system and you will sonarqube server. Add the sonarqube url on the Server URL and save. Now to go the pipeline, configure and add a build step

Add execute sonarcube scanner, under analysis properties, paste the key and save Go to manage Jenkins and configure systems, under server Authentication token, add click Jenkins. under Kind, select "secret text". Paste the secret key from sonarcube and give ID/description and save

Back to server auth token, elect sonar-token and save. Build now and confirm if sonarcube is scanning the code. Go to sonarcube webpage and check the details

SSh Docker VM
change hostname

sudo apt update

Install docker on ubuntu

sudo apt-get update
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg
Add Docker’s official GPG key:



sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
Use the following command to set up the repository:



echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
To install the latest version, run:



sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
Verify that the Docker Engine installation is successful by running the hello-world image:



sudo docker run hello-world
Now, we want to use docker services without the sudo command. So, in order to do so, we have to add this current "ubuntu" user to the docker group.


COPY
sudo usermod -aG docker ubuntu
newgrp docker
Now, you can use docker services without sudo command.

Switch to root user

nano /etc/ssh/sshd_config and uncomment pubkeyauthentication yes psswordAuthentication yes save and restart ssh service

Set password passwd ubuntu

From Jenkins, try to ssh to docker vm, log in successfully

On docker vm, ssh-keygen, ssh-copy-id ubuntu@

Number of keys added: 1

Back to Jenkins, configure system, under server group centres, Name Docker server Username ubuntu Password and save

Back to configure system add server list, Add server group , server name Docker-1 and server IP (docker) and save

Add build step

Remote shell and target server Docker-server, shell type "touch test.txt" and save. Run the pipeline and confirm that the test.txt is showing on Docker VM

Then create a Docker file in repo and create dir "mkdrir website"


Remove remote shell and add execute shell

Command
Scp  -r ./* ubuntu@ipadress of Docker:~/website and save

Build now and validate that dir website is in Docker VM

sudo user mod -aG docker ubuntu newgrp docker - make the ubutu have admin docker priviledge 

remote shell in Jenkins, Target server: Docker-servers

under shell
cd /home/ubuntu/website

docker build -t mywebsite .

docker run -d -p 8085:80 —name=Onix-website my website and save.

Build now and comfirm pipeline was succesful. Do check docker ps on docker VM to see the running ps and allow port 8085 on sg for Docker vm

Copy the public ip and paste on the browse with 8085 to validate the webpage
