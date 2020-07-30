## Getting Started with Ansible and Dynamic Amazon EC2 Inventory Management

### Summary of steps
**Step 1 to Step 4**
- Install python 3, pip and boto3
- Ansible is installed and has access to your Secret and Access key (via EC2 role or environment variable)
- ec2.py inventory script is downloaded and ec2.ini is configured
- ANSIBLE_HOSTS environment variable set
- ansible.cfg exists
- SSH agent is running (You can check with “ssh-add -L”)

### Step 1: Install Python 3, PIP and Boto3
```python
[ec2-user ~]$ yum list installed | grep -i python3
```
If Python 3 isn't already installed, then install the package using the yum package manager.
```python
[ec2-user ~]$ sudo yum install python3 -y
...
Dependencies Resolved

=========================================================================================================================================
 Package                              Arch                     Version                                Repository                    Size
=========================================================================================================================================
Installing:
 python3                              x86_64                   3.7.6-1.amzn2.0.1                      amzn2-core                    71 k
Installing for dependencies:
 python3-libs                         x86_64                   3.7.6-1.amzn2.0.1                      amzn2-core                   9.1 M
 python3-pip                          noarch                   9.0.3-1.amzn2.0.2                      amzn2-core                   1.9 M
 python3-setuptools                   noarch                   38.4.0-3.amzn2.0.6                     amzn2-core                   617 k

Transaction Summary
=========================================================================================================================================
Install  1 Package (+3 Dependent packages)

Total download size: 12 M
...
```
```python
[ec2-user@ip-172-31-35-226 ~]$ sudo yum install epel-release
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
192 packages excluded due to repository priority protections
Package epel-release-7-11.noarch already installed and latest version
Nothing to do
```

```python
[ec2-user@ip-172-31-35-226 ~]$ sudo yum install python-pip
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
192 packages excluded due to repository priority protections
Resolving Dependencies
--> Running transaction check
---> Package python2-pip.noarch 0:9.0.3-1.amzn2.0.2 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

=========================================================================================================================================
 Package                         Arch                       Version                                 Repository                      Size
=========================================================================================================================================
Installing:
 python2-pip                     noarch                     9.0.3-1.amzn2.0.2                       amzn2-core                     1.9 M

Transaction Summary
=========================================================================================================================================
Install  1 Package

Total download size: 1.9 M
```
```python
[ec2-user@ip-172-31-35-226 ~]$ sudo pip install pip --upgrade
WARNING: Running pip install with root privileges is generally not a good idea. Try `pip install --user` instead.
Collecting pip
  Downloading https://files.pythonhosted.org/packages/36/74/38c2410d688ac7b48afa07d413674afc1f903c1c1f854de51dc8eb2367a5/pip-20.2-py2.py3-none-any.whl (1.5MB)
    100% |████████████████████████████████| 1.5MB 738kB/s
Installing collected packages: pip
  Found existing installation: pip 9.0.3
    Uninstalling pip-9.0.3:
      Successfully uninstalled pip-9.0.3
Successfully installed pip-20.2
```
```python
[ec2-user@ip-172-31-35-226 ~]$ sudo pip install boto3
DEPRECATION: Python 2.7 reached the end of its life on January 1st, 2020. Please upgrade your Python as Python 2.7 is no longer maintained. pip 21.0 will drop support for Python 2.7 in January 2021. More details about Python 2 support in pip can be found at https://pip.pypa.io/en/latest/development/release-process/#python-2-support
Collecting boto3
  Downloading boto3-1.14.31-py2.py3-none-any.whl (129 kB)
     |████████████████████████████████| 129 kB 20.3 MB/s
Requirement already satisfied: jmespath<1.0.0,>=0.7.1 in /usr/lib/python2.7/site-packages (from boto3) (0.9.3)
Collecting s3transfer<0.4.0,>=0.3.0
  Downloading s3transfer-0.3.3-py2.py3-none-any.whl (69 kB)
     |████████████████████████████████| 69 kB 10.5 MB/s
Collecting botocore<1.18.0,>=1.17.31
  Downloading botocore-1.17.31-py2.py3-none-any.whl (6.4 MB)
     |████████████████████████████████| 6.4 MB 30.5 MB/s
Requirement already satisfied: futures<4.0.0,>=2.2.0; python_version == "2.7" in /usr/lib/python2.7/site-packages (from s3transfer<0.4.0,>=0.3.0->boto3) (3.0.5)
Requirement already satisfied: urllib3<1.26,>=1.20; python_version != "3.4" in /usr/lib/python2.7/site-packages (from botocore<1.18.0,>=1.17.31->boto3) (1.25.7)
Requirement already satisfied: python-dateutil<3.0.0,>=2.1 in /usr/lib/python2.7/site-packages (from botocore<1.18.0,>=1.17.31->boto3) (2.6.0)
Requirement already satisfied: docutils<0.16,>=0.10 in /usr/lib/python2.7/site-packages (from botocore<1.18.0,>=1.17.31->boto3) (0.12)
Requirement already satisfied: six>=1.5 in /usr/lib/python2.7/site-packages (from python-dateutil<3.0.0,>=2.1->botocore<1.18.0,>=1.17.31->boto3) (1.9.0)
Installing collected packages: botocore, s3transfer, boto3
  Attempting uninstall: botocore
    Found existing installation: botocore 1.13.36
    Uninstalling botocore-1.13.36:
      Successfully uninstalled botocore-1.13.36
  Attempting uninstall: s3transfer
    Found existing installation: s3transfer 0.1.12
    Uninstalling s3transfer-0.1.12:
      Successfully uninstalled s3transfer-0.1.12
ERROR: After October 2020 you may experience errors when installing or updating packages. This is because pip will change the way that it resolves dependency conflicts.

We recommend you use --use-feature=2020-resolver to test your packages with the new resolver before it becomes the default.

awscli 1.16.300 requires botocore==1.13.36, but you'll have botocore 1.17.31 which is incompatible.
awscli 1.16.300 requires s3transfer<0.3.0,>=0.2.0, but you'll have s3transfer 0.3.3 which is incompatible.
Successfully installed boto3-1.14.31 botocore-1.17.31 s3transfer-0.3.3
[ec2-user@ip-172-31-35-226 ~]$
```

Verify python version:
```python
[ec2-user@ip-172-31-35-226 ansible]$ sudo rm /usr/bin/python
[ec2-user@ip-172-31-35-226 ansible]$ sudo ln -s /usr/bin/python3 /usr/bin/python
[ec2-user@ip-172-31-35-226 ansible]$ python --version
Python 3.7.6
```
### Step 2: Set environment variables needed for Boto

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
### Step 3: Download EC2 external inventory script from Ansible to /etc/ansible/ec2.py and grant executable access
1. Download [EC2 external inventory script] (https://raw.githubusercontent.com/ansible/ansible/stable-2.9/contrib/inventory/ec2.py).
This script generates inventory that Ansible can understand by making API request to AWS EC2 using the Boto library.

```python
[ec2-user@ip-172-31-35-226 ansible]$ sudo curl https://raw.githubusercontent.com/ansible/ansible/stable-2.9/contrib/inventory/ec2.py --output /etc/ansible/ec2.py
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 73130  100 73130    0     0  2645k      0 --:--:-- --:--:-- --:--:-- 2645k
[ec2-user@ip-172-31-35-226 ansible]$ sudo chmod +x ec2.py
```
2. Copy the [ec2.ini](https://raw.githubusercontent.com/ansible/ansible/stable-2.9/contrib/inventory/ec2.ini) file to /etc/ansible/ec2.ini.
[ec2-user@ip-172-31-35-226 ansible]$ sudo curl https://raw.githubusercontent.com/ansible/ansible/stable-2.9/contrib/inventory/ec2.ini --output /etc/ansible/ec2.ini
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  9529  100  9529    0     0  29051      0 --:--:-- --:--:-- --:--:-- 29051

3. You can test the script by itself to make sure your config is correct:
```python
cd contrib/inventory
./ec2.py --list
```
### Step 4: Set environment variables for the inventory management script
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
3. Enable SSH agent forwarding
SSH agent is a program that runs in the background and keeps your key loaded into memory, so that you don't need to enter your passphrase every time you need to use the key. 
Using an SSH agent is the best way to authenticate with your end nodes, as this alleviates the need to copy your .pem files around. To [add an agent](https://developer.github.com/v3/guides/using-ssh-agent-forwarding/), do:
```python
[ec2-user@ip-172-31-35-226 .ssh]$ echo "$SSH_AUTH_SOCK"
/tmp/ssh-eGOTjPALATYS/agent.8078
[ec2-user@ip-172-31-35-226 .ssh]$ ssh-add julie.pem
[ec2-user@ip-172-31-35-226 .ssh]$ ssh-add -L
```




