## Getting Started with Ansible and Dynamic Amazon EC2 Inventory Management

1. Set environment variables needed for Boto

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
aws_access_key_id = AKIA5WMYFFB64O4H5LXT
aws_secret_access_key = rc9JG5abzKO1Sf7MY5HXuNn5aOJuYVYao4XctYjz
$ export AWS_ACCESS_KEY_ID='YOUR_AWS_API_KEY'
$ export AWS_SECRET_ACCESS_KEY='YOUR_AWS_API_SECRET_KEY'
$ export export AWS_REGION='ap-southeast-2'
```
2. Download EC2 external inventory script from Ansible to /etc/ansible/ec2.py and grant executable access
Download [EC2 external inventory script] (https://raw.githubusercontent.com/ansible/ansible/stable-2.9/contrib/inventory/ec2.py).
This script generates inventory that Ansible can understand by making API request to AWS EC2 using the Boto library.

```python
[ec2-user@ip-172-31-35-226 ansible]$ sudo curl https://raw.githubusercontent.com/ansible/ansible/stable-2.9/contrib/inventory/ec2.py --output /etc/ansible/ec2.py
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 73130  100 73130    0     0  2645k      0 --:--:-- --:--:-- --:--:-- 2645k
[ec2-user@ip-172-31-35-226 ansible]$ sudo chmod +x ec2.py
```


