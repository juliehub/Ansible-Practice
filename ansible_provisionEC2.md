The following section includes steps on how to provision AWS EC2 instances using EC2

### Step 1: Create AWS account
### Step 2: Install Ansible and Ansible EC2 module dependencies (Python3, PIP, Boto3)
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
Install python2-pip
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
Verify python version:
```python
[ec2-user@ip-172-31-1-214 ~]$ ls -l  /usr/bin/python
lrwxrwxrwx 1 root root 7 Jul 24 16:01 /usr/bin/python -> python2
[ec2-user@ip-172-31-35-226 ansible]$ sudo rm /usr/bin/python
[ec2-user@ip-172-31-35-226 ansible]$ sudo ln -s /usr/bin/python3 /usr/bin/python
[ec2-user@ip-172-31-35-226 ansible]$ python --version
Python 3.7.6
```
Install boto, boto3, and ansible
```python
[ec2-user@ip-172-31-1-214 ~]$ sudo pip install boto
WARNING: Running pip install with root privileges is generally not a good idea. Try `pip install --user` instead.
Collecting boto
  Downloading https://files.pythonhosted.org/packages/23/10/c0b78c27298029e4454a472a1919bde20cb182dab1662cec7f2ca1dcc523/boto-2.49.0-py2.py3-none-any.whl (1.4MB)
    100% |████████████████████████████████| 1.4MB 830kB/s
Installing collected packages: boto
Successfully installed boto-2.49.0
[ec2-user@ip-172-31-1-214 ~]$ sudo pip install boto3
WARNING: Running pip install with root privileges is generally not a good idea. Try `pip install --user` instead.
Collecting boto3
  Downloading https://files.pythonhosted.org/packages/7a/05/d52d446a5abcbb73f6661559943ae5d93c42ecb18977670a2d21718774ec/boto3-1.14.33-py2.py3-none-any.whl (129kB)
    100% |████████████████████████████████| 133kB 5.2MB/s
[ec2-user@ip-172-31-1-214 ~]$ sudo pip install ansible
...
Installing collected packages: ansible
  Running setup.py install for ansible ... done
Successfully installed ansible-2.9.11
```

### Step 3: Create SSH keys to connect to the EC2 instance after provisioning
```python
[ec2-user@ip-172-31-35-226 ~]$ ssh-keygen -t rsa -b 4096 -f ~/.ssh/ansible_ec2
Generating public/private rsa key pair.
```
### Step 4: Create Ansible structure (Alternatively, you can use Git (or SVN) to keep the version control of this directory)
```python
mkdir -p AWS_Ansible/group_vars/all/
cd AWS_Ansible
touch playbook.yml
```
### Step 5: Create Ansible Vault file to store the AWS Access and Secret keys
(The password provided here will be asked every time the playbook is executed or when editing the pass.yml file.)
```python
[ec2-user@ip-172-31-35-226 AWS_Ansible]$ ansible-vault create group_vars/all/pass.yml
New Vault password:
Confirm New Vault password:
```
**Edit the pass.yml file to include AWS access and secret keys**

Create the variables ec2_access_key and ec2_secret_key and set the values gathered after user creation (IAM).
```python
$ ansible-vault edit group_vars/all/pass.yml 
Vault password:
ec2_access_key: AAAAAAAAAAAAAABBBBBBBBBBBB                                      
ec2_secret_key: afjdfadgf$fgajk5ragesfjgjsfdbtirhf
```

### Step 6: Download playbook.yml
Copy [playbook.yml](https://github.com/juliehub/Ansible-Practice/blob/master/playbook.yml) to /home/ec2-user/AWS_Ansible/playbook.yml.

If you execute Ansible without the tags argument the creation tasks won’t be performed.

```python
[ec2-user@ip-172-31-1-214 AWS_Ansible]$ ansible-playbook playbook.yml --ask-vault-pass
Vault password:
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] ************************************************************************************************************************

TASK [Get instances information] ********************************************************************************************************
ok: [localhost]

TASK [Instances ID] *********************************************************************************************************************
ok: [localhost] => (item={u'root_device_type': u'ebs', u'private_dns_name': u'ip-172-31-1-214.ap-southeast-2.compute.internal', u'cpu_options': {u'threads_per_core': 1, u'core_count': 1}, u'security_groups': [{u'group_id': u'sg-035c6a8e5df7c0cff', u'group_name': u'launch-wizard-5'}], u'monitoring': {u'state': u'disabled'}, u'subnet_id': u'subnet-9bb052fd', u'ebs_optimized': False, u'state': {u'code': 16, u'n
```

### Step 7: Run Ansible to provision the EC2 instance
```python
[ec2-user@ip-172-31-1-214 AWS_Ansible]$ ansible-playbook playbook.yml --ask-vault-pass --tags create_ec2
Vault password:
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] ************************************************************************************************************************

TASK [Get instances information] ********************************************************************************************************
ok: [localhost]

TASK [Instances ID] *********************************************************************************************************************
ok: [localhost] => (item={u'root_device_type': u'ebs', u'private_dns_name': u'ip-172-31-1-214.ap-southeast-2.compute.internal', u'cpu_options': {u'threads_per_core': 1, u'core_count': 1}, u'security_groups': [{u'group_id': u'sg-035c6a8e5df7c0cff', u'group_name': u'launch-wi
...



TASK [Upload public key to AWS] *********************************************************************************************************
changed: [localhost]

TASK [Create security group] ************************************************************************************************************
changed: [localhost]

TASK [Provision instance(s)] ************************************************************************************************************
changed: [localhost]

PLAY RECAP ******************************************************************************************************************************
localhost                  : ok=5    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```
### Step 8: Connect to the EC2 instance via SSH
**1. Get public IP**
```python
[ec2-user@ip-172-31-1-214 AWS_Ansible]$ ansible-playbook playbook.yml --ask-vault-pass
Vault password:
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] ************************************************************************************************************************

TASK [Get instances information] ********************************************************************************************************
ok: [localhost]

TASK [Instances ID] *********************************************************************************************************************
...
...
85'}], u'launch_time': u'2020-08-01T03:40:25+00:00', u'instance_id': u'i-08cb9f4caaf913d89', u'instance_type': u't2.micro', u'architecture': u'x86_64', u'ena_support': True, u'private_ip_address': u'172.31.11.216', u'vpc_id': u'vpc-9f85bef8', u'product_codes': []}) => {
    "msg": "ID: i-08cb9f4caaf913d89 - State: running - Public DNS: ec2-54-253-20-33.ap-southeast-2.compute.amazonaws.com"
}


```
**2. Connect to the EC2 instance via SSH**
```python
[ec2-user@ip-172-31-1-214 AWS_Ansible]$ ssh -i ~/.ssh/ansible_ec2 ec2-54-253-20-33.ap-southeast-2.compute.amazonaws.com
The authenticity of host 'ec2-54-253-20-33.ap-southeast-2.compute.amazonaws.com (172.31.11.216)' can't be established.
ECDSA key fingerprint is SHA256:OlTXQYo3JuSc2lDUeuWTv2QOE2s8FOq0QTFmrbx043Q.
ECDSA key fingerprint is MD5:32:7f:19:41:03:45:06:d0:5a:c1:12:12:67:7f:f0:c7.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'ec2-54-253-20-33.ap-southeast-2.compute.amazonaws.com,172.31.11.216' (ECDSA) to the list of known hosts.

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
14 package(s) needed for security, out of 31 available
Run "sudo yum update" to apply all updates.
```
