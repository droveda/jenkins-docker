# Setting up the environment and Virtual Machine
## Virtual machine with Vagrant
## Pre-requisites
* http://vagrantup.com
* https://www.virtualbox.org
* Understand Vagrant
---
## Starting the VM
* vagrant plugin install vagrant-disksize (if you got an issue running this on mac, you can temp disable IPv6)
* vagrant up
* vagrant ssh
    * ps -fe | grep -i mysql (check if mysql is running)
    * mysql -u devops -p (password: mestre) show databases
    * mysql -u devops_dev -p (password: mestre) show databses

## Jenins Installation
* cd /vagrant/scripts
* sudo ./jenkins.sh
* access: 192.168.33.10:8080
* sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    * if you got issues when trying to install jenkins this can help: https://chrisjean.com/fix-apt-get-update-the-following-signatures-couldnt-be-verified-because-the-public-key-is-not-available/
    * sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 40976EAF437D05B5

## Credentials
* Name: Jenkins Alura
* User: alura
* Password: mestre123
* Email: aluno@alura.com.br

## Aditional steps
* sudo usermod -aG docker $USER
* sudo usermod -aG docker jenkins
* logout from the Vagrant VM
* execute: vagrant reload


<br />
<br />

# Steps to create git settings and code versioning
* create an account on github
    * ssh-keygen -t rsa -b 4096 -C "email@gmail.com"
    * cat ~/.ssh/id_rsa.pub
* configure the key on github
    * git config --global user.name "username"
    * git config --global user.email "email@gmail.com"
    * ssh -T git@github.com (test if the rsa public key was correctly installed)
* Download the code and copy it to the shared directory app
    * cd /vagrant/jenkins-todo-list
    * git init
    * git add .
    * git commit -m "Initial commit"
    * git log
* Create the repository on github: jenkins-todo-list
    * git remote add origin git@github.com:username/jenkins-todo-list.git
    * git push -u origin master

<br />
<br />

# Jenkins Initial setting
## Setting up the private key created on the VM environment on Jenkins
* cat ~/.ssh/id_rsa
* Credentials -> Jenkins -> Global Credentials -> Add Credentials -> Kind: SSH Username with private key
    * id: gitHub-ssh
    * description: gitHub-ssh
    * username: git
    * private key: add the private key that you got from the VM (cat ~/.ssh/id_rsa)
    * Passphrase: leave it in blank
## Setting up the first job
* Create new jobs -> django-todolist-build -> Freestyle project:
* This job will build the project and register the image on the repository
    * SCM:
        * Git: git@github.com:username/reponame.git [SSH] (be sure to use this one and not the HTTPS)
        * Credentials: git (github-ssh)
        * Branch: master
    * Build Triggers
        * Pool SCM: * * * * *
    * Build env
        * Delete workspace before build starts
    * Save
        * Validate logs in: Git Polling Log


<br />

# Setting up docker daemon
* sudo mkdir -p /etc/systemd/system/docker.service.d/
* sudo vi /etc/systemd/system/docker.service.d/override.conf
    * [Service]
    * ExecStart=
    * ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2376
* sudo systemctl daemon-reload
* sudo systemctl restart docker.service

## Jenkins with docker
* Install docker plugin on Jenkins

<br />

# Install Sonarqube
* docker run -d --name sonarqube -p 9000:9000 sonarqube:lts
    * Access: http://192.168.33.10:9000
    * User + Password = admin
    * Create the token to admin user: Administration -> Security -> Users
        * Tokens:
            * Name: jenkins-todolist
            * You need to Copy the token (It is important to keep this info): 322e0cb13e6bdba56113eee8d500631b8e06fe12
    * Create the sonar project
    * Create a coverage job with:
        * git: git@github.com:{username}/{repository}.git
        * branch: master
        * Pool SCM: * * * * *
        * Delete workspace before build starts
        * Execute script:
            * #!/bin/bash
            * #downloading sonarqube
            * wget https://s3.amazonaws.com/caelum-online-public/1110-jenkins/05/sonar-scanner-cli-3.3.0.1492-linux.zip 
            * #Extract the scanner
            * unzip sonar-scanner-cli-3.3.0.1492-linux.zip
            * #running the scanner
            * ./sonar-scanner-3.3.0.1492-linux/bin/sonar-scanner   -X \
          -Dsonar.projectKey=jenkins-todolist \
          -Dsonar.sources=. \
          -Dsonar.host.url=http://192.168.33.10:9000 \
          -Dsonar.login=322e0cb13e6bdba56113eee8d500631b8e06fe12


* sonar-scanner \
  -Dsonar.projectKey=jenkins-todolist \
  -Dsonar.sources=. \
  -Dsonar.host.url=http://192.168.33.10:9000 \
  -Dsonar.login=322e0cb13e6bdba56113eee8d500631b8e06fe12