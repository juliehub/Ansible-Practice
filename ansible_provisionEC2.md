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
Copy [playbook.yml](https://github.com/juliehub/Ansible-Practice/blob/master/playbook.yml) to /home/ec2-user/AWS_Ansible/playbook.yml

### Step 7: Run Ansible to provision the EC2 instance
```python
[ec2-user@ip-172-31-1-214 AWS_Ansible]$ ansible-playbook playbook.yml --ask-vault-pass
Vault password:
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] ************************************************************************************************************************

TASK [Get instances facts] **************************************************************************************************************
[DEPRECATION WARNING]: The 'ec2_instance_facts' module has been renamed to 'ec2_instance_info'. This feature will be removed in version
2.13. Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [localhost]

TASK [Instances ID] *********************************************************************************************************************
ok: [localhost] => (item={u'root_device_type': u'ebs', u'private_dns_name': u'ip-172-31-1-214.ap-southeast-2.compute.internal', u'cpu_options': {u'threads_per_core': 1, u'core_count': 1}, u'security_groups': [{u'group_id': u'sg-035c6a8e5df7c0cff', u'group_name': u'launch-wizard-5'}], u'monitoring': {u'state': u'disabled'}, u'subnet_id': u'subnet-9bb052fd', u'ebs_optimized': False, u'state': {u'code': 16, u'name': u'running'}, u'source_dest_check': True, u'client_token': u'', u'virtualization_type': u'hvm', u'root_device_name': u'/dev/xvda', u'public_ip_address': u'3.25.174.4', u'tags': {u'Name': u'ansible-control'}, u'key_name': u'julie2', u'image_id': u'ami-0ded330691a314693', u'state_transition_reason': u'', u'hibernation_options': {u'configured': False}, u'capacity_reservation_specification': {u'capacity_reservation_preference': u'open'}, u'public_dns_name': u'ec2-3-25-174-4.ap-southeast-2.compute.amazonaws.com', u'block_device_mappings': [{u'ebs': {u'status': u'attached', u'delete_on_termination': True, u'attach_time': u'2020-07-31T22:47:05+00:00', u'volume_id': u'vol-0add7cceafce1e497'}, u'device_name': u'/dev/xvda'}], u'metadata_options': {u'http_endpoint': u'enabled', u'state': u'applied', u'http_tokens': u'optional', u'http_put_response_hop_limit': 1}, u'placement': {u'availability_zone': u'ap-southeast-2b', u'tenancy': u'default', u'group_name': u''}, u'ami_launch_index': 0, u'hypervisor': u'xen', u'network_interfaces': [{u'status': u'in-use', u'description': u'', u'interface_type': u'interface', u'ipv6_addresses': [], u'groups': [{u'group_id': u'sg-035c6a8e5df7c0cff', u'group_name': u'launch-wizard-5'}], u'association': {u'public_ip': u'3.25.174.4', u'public_dns_name': u'ec2-3-25-174-4.ap-southeast-2.compute.amazonaws.com', u'ip_owner_id': u'amazon'}, u'source_dest_check': True, u'private_dns_name': u'ip-172-31-1-214.ap-southeast-2.compute.internal', u'subnet_id': u'subnet-9bb052fd', u'network_interface_id': u'eni-06d417170dc905982', u'attachment': {u'status': u'attached', u'device_index': 0, u'attachment_id': u'eni-attach-0f4bbac15718c4f7d', u'delete_on_termination': True, u'attach_time': u'2020-07-31T22:47:04+00:00'}, u'private_ip_addresses': [{u'private_ip_address': u'172.31.1.214', u'private_dns_name': u'ip-172-31-1-214.ap-southeast-2.compute.internal', u'association': {u'public_ip': u'3.25.174.4', u'public_dns_name': u'ec2-3-25-174-4.ap-southeast-2.compute.amazonaws.com', u'ip_owner_id': u'amazon'}, u'primary': True}], u'mac_address': u'02:38:20:75:38:e0', u'private_ip_address': u'172.31.1.214', u'vpc_id': u'vpc-9f85bef8', u'owner_id': u'941453813885'}], u'launch_time': u'2020-07-31T22:47:04+00:00', u'instance_id': u'i-0763a9df54e76229a', u'instance_type': u't2.micro', u'architecture': u'x86_64', u'ena_support': True, u'private_ip_address': u'172.31.1.214', u'vpc_id': u'vpc-9f85bef8', u'product_codes': []}) => {
    "msg": "ID: i-0763a9df54e76229a - State: running - Public DNS: ec2-3-25-174-4.ap-southeast-2.compute.amazonaws.com"
}
ok: [localhost] => (item={u'root_device_type': u'ebs', u'private_dns_name': u'ip-172-31-35-226.ap-southeast-2.compute.internal', u'cpu_options': {u'threads_per_core': 1, u'core_count': 1}, u'security_groups': [{u'group_id': u'sg-038f8dbf00af736ea', u'group_name': u'launch-wizard-4'}], u'state_reason': {u'message': u'Client.UserInitiatedShutdown: User initiated shutdown', u'code': u'Client.UserInitiatedShutdown'}, u'monitoring': {u'state': u'disabled'}, u'subnet_id': u'subnet-5affe313', u'ebs_optimized': False, u'state': {u'code': 80, u'name': u'stopped'}, u'source_dest_check': True, u'client_token': u'', u'virtualization_type': u'hvm', u'root_device_name': u'/dev/xvda', u'tags': {u'Name': u'ansible-master'}, u'key_name': u'julie2', u'image_id': u'ami-0a58e22c727337c51', u'state_transition_reason': u'User initiated (2020-07-31 22:46:30 GMT)', u'hibernation_options': {u'configured': False}, u'capacity_reservation_specification': {u'capacity_reservation_preference': u'open'}, u'public_dns_name': u'', u'block_device_mappings': [{u'ebs': {u'status': u'attached', u'delete_on_termination': True, u'attach_time': u'2020-07-28T03:09:32+00:00', u'volume_id': u'vol-017fc9163f0dd15d9'}, u'device_name': u'/dev/xvda'}], u'metadata_options': {u'http_endpoint': u'enabled', u'state': u'applied', u'http_tokens': u'optional', u'http_put_response_hop_limit': 1}, u'placement': {u'availability_zone': u'ap-southeast-2a', u'tenancy': u'default', u'group_name': u''}, u'ami_launch_index': 0, u'hypervisor': u'xen', u'network_interfaces': [{u'status': u'in-use', u'description': u'', u'subnet_id': u'subnet-5affe313', u'interface_type': u'interface', u'owner_id': u'941453813885', u'ipv6_addresses': [], u'network_interface_id': u'eni-0dab3013961d9acdb', u'attachment': {u'status': u'attached', u'device_index': 0, u'attachment_id': u'eni-attach-0c8cc15ae3e55be79', u'delete_on_termination': True, u'attach_time': u'2020-07-28T03:09:31+00:00'}, u'private_ip_addresses': [{u'private_ip_address': u'172.31.35.226', u'private_dns_name': u'ip-172-31-35-226.ap-southeast-2.compute.internal', u'primary': True}], u'mac_address': u'06:4f:3f:d8:06:6e', u'private_ip_address': u'172.31.35.226', u'vpc_id': u'vpc-9f85bef8', u'groups': [{u'group_d': u'sg-038f8dbf00af736ea', u'group_name': u'launch-wizard-4'}], u'source_dest_check': True, u'private_dns_name': u'ip-172-31-35-226.ap-southeast-2.compute.internal'}], u'launch_time': u'2020-07-28T03:09:31+00:00', u'instance_id': u'i-01baef54d59522f57', u'instance_type': u't2.micro', u'architecture': u'x86_64', u'ena_support': True, u'private_ip_address': u'172.31.35.226', u'vpc_id': u'vpc-9f85bef8', u'product_codes': []}) => {
    "msg": "ID: i-01baef54d59522f57 - State: stopped - Public DNS: "
```

### Connect to the EC2 instance via SSH
