# Ansible-Practice
This repository contains practice instructions how to automate an Ansible playbook deployment using Amazon Elastic Compute Cloud (Amazon EC2) and GitHub. Please refer to the link in [AWS Blog](https://aws.amazon.com/blogs/infrastructure-and-automation/automate-ansible-playbook-deployment-amazon-ec2-github/) for more details.

### Process
![Alt Text](https://github.com/juliehub/Ansible-Practice/blob/master/ansible_process.jpg)

### Prerequisites
- An AWS account
- An Amazon EC2 key pair
- An Amazon EC2 instance running an Amazon Linux 2 AMI (Ansible "master")
- A GitHub repository to store [playbooks](https://github.com/juliehub/Ansible-Practice)
- A security group that allows SSH (Secure Shell) and HTTPS access (To be more secured, edit the source IP with your own IP address for SSH rule)

![Alt Text](https://github.com/juliehub/Ansible-Practice/blob/master/ansible_sg.jpg)

# References:
https://aws.amazon.com/blogs/infrastructure-and-automation/automate-ansible-playbook-deployment-amazon-ec2-github/
https://aws.amazon.com/blogs/apn/getting-started-with-ansible-and-dynamic-amazon-ec2-inventory-management/
https://docs.ansible.com/ansible/latest/scenario_guides/guide_aws.html
