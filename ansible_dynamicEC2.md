# Getting Started with Ansible and Dynamic Amazon EC2 Inventory Management

## Download EC2 external inventory script from Ansible
If you use Amazon Web Services EC2, maintaining an inventory file might not be the best approach, 
because hosts may come and go over time, be managed by external applications, or you might even be using AWS autoscaling. 
Download [EC2 external inventory script] (https://raw.githubusercontent.com/ansible/ansible/stable-2.9/contrib/inventory/ec2.py).
This script generates inventory that Ansible can understand by making API request to AWS EC2 using the Boto library.

```python
[ec2-user@ip-172-31-35-226 ansible]$ sudo curl https://raw.githubusercontent.com/ansible/ansible/stable-2.9/contrib/inventory/ec2.py --output /etc/ansible/ec2.py
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 73130  100 73130    0     0  2645k      0 --:--:-- --:--:-- --:--:-- 2645k
[ec2-user@ip-172-31-35-226 ansible]$
```

