# Ansible-Practice
This repository contains practice instructions how to automate an Ansible playbook deployment using Amazon Elastic Compute Cloud (Amazon EC2) and GitHub. Please refer to the link in [AWS Blog](https://aws.amazon.com/blogs/infrastructure-and-automation/automate-ansible-playbook-deployment-amazon-ec2-github/) for more details.

## Process
![Alt Text](https://github.com/juliehub/Ansible-Practice/blob/master/ansible_process.jpg)

## Prerequisites
- An AWS account
- An Amazon EC2 key pair
- An Amazon EC2 instance running an Amazon Linux 2 AMI (Ansible "master")
- A GitHub repository to store [playbooks](https://github.com/juliehub/Ansible-Practice)
- A security group that allows SSH (Secure Shell) and HTTPS access (To be more secured, edit the source IP with your own IP address for SSH rule)

![Alt Text](https://github.com/juliehub/Ansible-Practice/blob/master/ansible_sg.jpg)

## Walkthrough
### Step 1: Set up webhook processing
We use NGINX as a reverse proxy to route the request to an Express server.
1. Connect to Ansible-Master EC2 instance, enable the Extra Packages for Enterprise Linux (EPEL) repository by running the following command.
```python
[ec2-user@ip-172-31-35-226 ~]$ sudo amazon-linux-extras install epel
Installing epel-release
...
=========================================================================================================================
 Package                  Arch                    Version                     Repository                             Size
=========================================================================================================================
Installing:
 epel-release             noarch                  7-11                        amzn2extra-epel                        15 k

Transaction Summary
=========================================================================================================================
Install  1 Package
...
```
2. Update all installed packages
```python
sudo yum update -y
```
3. Install Ansible, NGINX, and Git
```python
[ec2-user@ip-172-31-35-226 ~]$ sudo yum install ansible -y
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
amzn2-core                                                                                            | 3.7 kB  00:00:00
192 packages excluded due to repository priority protections
Resolving Dependencies
...
=========================================================================================================================
 Package                     Arch                   Version                               Repository                 Size
=========================================================================================================================
Installing:
 ansible                     noarch                 2.9.10-1.el7                          epel                       17 M
...
```

```python
[ec2-user@ip-172-31-35-226 ~]$ sudo yum install nginx -y
...
Dependencies Resolved

=========================================================================================================================
 Package                           Arch              Version                             Repository                  Size
=========================================================================================================================
Installing:
 nginx                             x86_64            1:1.16.1-1.el7                      epel                       562 k
...
```

```python
[ec2-user@ip-172-31-35-226 ~]$ sudo yum install git -y
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
...
=========================================================================================================================
 Package                   Arch                 Version                               Repository                     Size
=========================================================================================================================
Installing:
 git                       x86_64               2.23.3-1.amzn2.0.1                    amzn2-core                    135 k
...
```

### Step 2. Install Node.js and set up the Express server
1. Node.js.
```python
$ sudo curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 13226  100 13226    0     0   461k      0 --:--:-- --:--:-- --:--:--  461k
=> Downloading nvm from git to '/home/ec2-user/.nvm'
=> Cloning into '/home/ec2-user/.nvm'...
remote: Enumerating objects: 278, done.
remote: Counting objects: 100% (278/278), done.
remote: Compressing objects: 100% (245/245), done.
remote: Total 278 (delta 32), reused 95 (delta 20), pack-reused 0
Receiving objects: 100% (278/278), 142.15 KiB | 362.00 KiB/s, done.
Resolving deltas: 100% (32/32), done.
=> Compressing and cleaning up git repository

=> Appending nvm source string to /home/ec2-user/.bashrc
=> Appending bash_completion source string to /home/ec2-user/.bashrc
=> Close and reopen your terminal to start using nvm or run the following to use it now:

export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```
```python
$ . ~/.nvm/nvm.sh
$ [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
$ [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
```
```python
$ nvm install node
Downloading and installing node v14.6.0...
Downloading https://nodejs.org/dist/v14.6.0/node-v14.6.0-linux-x64.tar.xz...
################################################################################################################# 100.0%
Computing checksum with sha256sum
Checksums matched!
Now using node v14.6.0 (npm v6.14.6)
Creating default alias: default -> node (-> v14.6.0)
```
2. Choose a location for the Express server. In this example, we create a directory called `server` to store the relevant files.

```python
$ pwd
/home/ec2-user
$ mkdir server && cd server
$ npm install express
npm WARN saveError ENOENT: no such file or directory, open '/home/ec2-user/server/package.json'
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN enoent ENOENT: no such file or directory, open '/home/ec2-user/server/package.json'
npm WARN server No description
npm WARN server No repository field.
npm WARN server No README data
npm WARN server No license field.

+ express@4.17.1
added 50 packages from 37 contributors and audited 50 packages in 3.946s
found 0 vulnerabilities
```

### Step 3. Set up a deploy key for your repository

### Step 4. Configure NGINX to route traffic

### Step 5. Set up GitHub to configure the webhook

# References:
https://aws.amazon.com/blogs/infrastructure-and-automation/automate-ansible-playbook-deployment-amazon-ec2-github/
https://aws.amazon.com/blogs/apn/getting-started-with-ansible-and-dynamic-amazon-ec2-inventory-management/
https://docs.ansible.com/ansible/latest/scenario_guides/guide_aws.html
