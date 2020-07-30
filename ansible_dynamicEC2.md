## Getting Started with Ansible and Dynamic Amazon EC2 Inventory Management

### Step 1: Set environment variables needed for Boto

```python
[ec2-user@ip-172-31-35-226 .aws]$ pwd
/home/ec2-user/
[ec2-user@ip-172-31-35-226 ~]$ mkdir .aws
[ec2-user@ip-172-31-35-226 ~]$ cd .aws
[ec2-user@ip-172-31-35-226 .aws]$ vi config
[ec2-user@ip-172-31-35-226 .aws]$ cat config
[default]
region = ap-southeast-2
[ec2-user@ip-172-31-35-226 .aws]$ vi credentials
[ec2-user@ip-172-31-35-226 .aws]$ cat credentials
[default]
aws_access_key_id = YOUR_AWS_API_KEY
aws_secret_access_key = YOUR_AWS_API_SECRET_KEY
$ export AWS_ACCESS_KEY_ID='YOUR_AWS_API_KEY'
$ export AWS_SECRET_ACCESS_KEY='YOUR_AWS_API_SECRET_KEY'
$ export export AWS_REGION='ap-southeast-2'
```
### Step 2: Download EC2 external inventory script from Ansible to /etc/ansible/ec2.py and grant executable access
Download [EC2 external inventory script] (https://raw.githubusercontent.com/ansible/ansible/stable-2.9/contrib/inventory/ec2.py).
This script generates inventory that Ansible can understand by making API request to AWS EC2 using the Boto library.

```python
[ec2-user@ip-172-31-35-226 ansible]$ sudo curl https://raw.githubusercontent.com/ansible/ansible/stable-2.9/contrib/inventory/ec2.py --output /etc/ansible/ec2.py
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 73130  100 73130    0     0  2645k      0 --:--:-- --:--:-- --:--:-- 2645k
[ec2-user@ip-172-31-35-226 ansible]$ sudo chmod +x ec2.py
```
### Step 3: Set environment variables for the inventory management script
1. Set `ANSIBLE_HOSTS` to use the dynamic EC2 script instead of a static /etc/ansible/hosts file.
```python
 $ export ANSIBLE_HOSTS=/etc/ansible/ec2.py
```
2. Edit `/etc/ansible/ec2.py` to include the path to the ec2.ini config file is defined correctly at the top of the script.
```python
[ec2-user@ip-172-31-35-226 ansible]$ sudo vi /etc/ansible/ec2.py
[ec2-user@ip-172-31-35-226 ansible]$ head -10 ec2.py
#!/usr/bin/env python

export EC2_INI_PATH=/etc/ansible/ec2.ini

'''
EC2 external inventory script
=================================

Generates inventory that Ansible can understand by making API request to
AWS EC2 using the Boto library.
[ec2-user@ip-172-31-35-226 ansible]$
```


