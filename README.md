# Setting up the environment and Virtual Machine
## Virtual machine with Vagrant
## Pre-requisites
* http://vagrantup.com
* https://www.virtualbox.org
* Understand Vagrant
---
## Starting the VM
* vagrant plugin install vagrant-disksize
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


vagrant plugin install vagrant-disksize --plugin-clean-sources --plugin-source https://gems.corp.my-company.com/

<br />
<br />



# Steps to create get setting and code versioning
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
    