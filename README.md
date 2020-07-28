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

===========================================================================================================================
 Package                             Arch              Version                             Repository                  Size
===========================================================================================================================
Installing:
 nginx                               x86_64            1:1.16.1-1.el7                      epel                       562 k
...
```

```python
[ec2-user@ip-172-31-35-226 ~]$ sudo yum install git -y
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
...
===========================================================================================================================
 Package                     Arch                 Version                               Repository                     Size
===========================================================================================================================
Installing:
 git                         x86_64               2.23.3-1.amzn2.0.1                    amzn2-core                    135 k
...
```


# References:
https://aws.amazon.com/blogs/infrastructure-and-automation/automate-ansible-playbook-deployment-amazon-ec2-github/
https://aws.amazon.com/blogs/apn/getting-started-with-ansible-and-dynamic-amazon-ec2-inventory-management/
https://docs.ansible.com/ansible/latest/scenario_guides/guide_aws.html
