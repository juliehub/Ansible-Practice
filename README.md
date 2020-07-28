# Ansible-Practice
This repository contains practice instructions on how to automate an Ansible playbook deployment using Amazon Elastic Compute Cloud (Amazon EC2) and GitHub. Please refer to the link in [AWS Blogs](https://aws.amazon.com/blogs/infrastructure-and-automation/automate-ansible-playbook-deployment-amazon-ec2-github/) for more details.

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
1. Connect to Ansible-Master EC2 instance, **enable the Extra Packages for Enterprise Linux (EPEL) repository** by running the following command.
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
**2. Update all installed packages**
```python
sudo yum update -y
```
**3. Install Ansible, NGINX, and Git**
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
1. **Install Node.js**
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
2. Choose a location for the Express server. In this example, we **create a directory called `server`** to store the relevant files.

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
3. Setup [/home/ec2-user/server/app.js](https://github.com/juliehub/Ansible-Practice/blob/master/app.js) that runs the `ansible-pull` command to pull and run the playbook [local.yml](https://github.com/juliehub/Ansible-Practice/blob/master/local.yml) file from a GitHub repository. 

The server is configured to listen on port **8080** because the NGINX configuration needs to know where to route the traffic that it receives. 

```python
var express = require('express');
var app = express();
const util = require('util');
const exec = util.promisify(require('child_process').exec);

app.post('/', function(req, res){
    try {
        console.log('executing deployment...');
        exec("ansible-pull -U git@github.com:juliehub/Ansible-Practice.git local.yml", (error, stdout, stderr) => {
                if (error) {
                console.log(`error: ${error.message}`);
                return;
        }
        if (stderr) {
                console.log(`stderr: ${stderr}`);
                return;
        }

        console.log(`stdout: ${stdout}`);

        });
    } catch (e) {
        console.log(e);
    }

    res.json({ received: true });
});

app.listen(8080, '127.0.0.1');
```
4. Clone that repository to your local working directory and create a playbook `local.yml`
```python
[ec2-user@ip-172-31-35-226 ~]$ pwd
/home/ec2-user
[ec2-user@ip-172-31-35-226 ~]$ git clone git@github.com:juliehub/Ansible-Practice.git
Cloning into 'Ansible-Practice'...
remote: Enumerating objects: 68, done.
remote: Counting objects: 100% (68/68), done.
remote: Compressing objects: 100% (68/68), done.
remote: Total 68 (delta 35), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (68/68), 104.76 KiB | 263.00 KiB/s, done.
Resolving deltas: 100% (35/35), done.
```
```python
[ec2-user@ip-172-31-35-226 ~]$ cd Ansible-Practice/
[ec2-user@ip-172-31-35-226 Ansible-Practice]$ vi local.yml
[ec2-user@ip-172-31-35-226 Ansible-Practice]$ cat local.yml
- hosts: localhost
  become: true
  tasks:
  - name: Install packages
    apt: name={{item}}
    with_items:
      - htop
      - mc
      - tmux
```
5. Commit `local.yml` to our respository
```python
[ec2-user@ip-172-31-35-226 Ansible-Practice]$ git add local.yml
[ec2-user@ip-172-31-35-226 Ansible-Practice]$ git commit -m "add local.yml"
[master 749befb] add local.yml
 Committer: EC2 Default User <ec2-user@ip-172-31-35-226.ap-southeast-2.compute.internal>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly. Run the
following command and follow the instructions in your editor to edit
your configuration file:

    git config --global --edit

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 1 file changed, 9 insertions(+)
 create mode 100644 local.yml
[ec2-user@ip-172-31-35-226 Ansible-Practice]$ git push origin master
Warning: Permanently added the RSA host key for IP address '52.64.108.95' to the list of known hosts.
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 407 bytes | 407.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To github.com:juliehub/Ansible-Practice.git
   3fda52b..749befb  master -> master
```

6. Run the Express server.
```python
[ec2-user@ip-172-31-35-226 server]$ ls -l app.js
-rw-rw-r-- 1 ec2-user ec2-user 762 Jul 28 06:04 app.js
[ec2-user@ip-172-31-35-226 server]$ node app.js
```
### Step 3. Set up a deploy key for your repository
1. **Create an SSH key** on your instance. In this example, replace <your_email@example.com> with your email address.
```python
[ec2-user@ip-172-31-35-226 ~]$ ssh-keygen -t rsa -b 4096 -C your_email@example.com
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ec2-user/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ec2-user/.ssh/id_rsa.
Your public key has been saved in /home/ec2-user/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:mt6FIlZnlJZ48X/1pudLFok17rQ7fibgotFyPFA+bXk your_email@example.com
The key's randomart image is:
+---[RSA 4096]----+
|        .        |
|       . =       |
|      . * o    o.|
|       + o o .+.+|
|      . S o =.E=o|
|     . = = o.oooo|
|    o + + *. ..=.|
|   . o o =... +++|
|      . o. .  .*=|
+----[SHA256]-----+

```
2. When the key is created, run the following code.
```python
[ec2-user@ip-172-31-35-226 ~]$ eval "$(ssh-agent -s)"
Agent pid 21682
[ec2-user@ip-172-31-35-226 ~]$ ps -ef|grep 21682|grep -v grep
ec2-user 21682     1  0 06:08 ?        00:00:00 ssh-agent -s
```
3. # Add GitHub to known_hosts
```python
[ec2-user@ip-172-31-35-226 .ssh]$ pwd
/home/ec2-user/.ssh
[ec2-user@ip-172-31-35-226 .ssh]$ ssh-keyscan github.com >> known_hosts
# github.com:22 SSH-2.0-babeld-5a455904
# github.com:22 SSH-2.0-babeld-5a455904
# github.com:22 SSH-2.0-babeld-5a455904
[ec2-user@ip-172-31-35-226 .ssh]$ cat known_hosts
github.com ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAq2A7hRGmdnm9tUDbO9IDSwBK6TbQa+PXYPCPy6rbTrTtw7PHkccKrpp0yVhp5HdEIcKr6pLlVDBfOLX9QUsyCOV0wzfjIJNlGEYsdlLJizHhbn2mUjvSAHQqZETYP81eFzLQNnPHt4EVVUh7VfDESU84KezmD5QlWpXLmvU31/yMf+Se8xhHTvKSCZIFImWwoG6mbUoWf9nzpIoaSjB+weqqUUmpaaasXVal72J+UX2B+2RPW3RcT0eOzQgqlJL3RKrTJvdsjE3JEAvGq3lGHSZXy28G3skua2SmVi/w4yCE6gbODqnTWlg7+wC604ydGXA8VJiS5ap43JXiUFFAaQ==
```


### Step 4. Configure NGINX to route traffic
1. Use the following basic configuration to listen on port 80 and route traffic to the port that the Express server listens to.
```python
[ec2-user@ip-172-31-35-226 nginx]$ pwd
/etc/nginx
[ec2-user@ip-172-31-35-226 nginx]$ sudo cp -p nginx.conf nginx.conf.bak
[ec2-user@ip-172-31-35-226 nginx]$ sudo vi nginx.conf
```
```python
server {
    listen       80 default_server;
    listen       [::]:80 default_server;
    server_name _;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    error_page 404 /404.html;
        location = /40x.html {
    }

    error_page 500 502 503 504 /50x.html;
        location = /50x.html {
    }
}
```
2. **Start NGINX**
```python
[ec2-user@ip-172-31-35-226 nginx]$ sudo systemctl start nginx
[ec2-user@ip-172-31-35-226 nginx]$ sudo systemctl enable nginx
Created symlink from /etc/systemd/system/multi-user.target.wants/nginx.service to /usr/lib/systemd/system/nginx.service.
```

### Step 5. Set up GitHub to configure the webhook
1. Log in to your GitHub account, navigate to **Settings**, then **SSH and GPG Keys**
2. Add **New SSH key**, copy and paste the public key from `/home/etc-user/.ssh/id_rsa.pub` to Github
```python
[ec2-user@ip-172-31-35-226 .ssh]$ pwd
/home/ec2-user/.ssh
[ec2-user@ip-172-31-35-226 .ssh]$ cat id_rsa.pub
```
3. Go to your Ansible repository, choose **Add webhook** on the Webhooks tab.

4. Copy and paste your **EC2 instance’s public IP address** into the **Payload URL** section.
This adds the webhook that is triggered when a push event occurs.
When the webhook is created and a request is sent to the EC2 instance, 
the Recent Deliveries section looks like this:

## Cleanup
To remove your instance after provisioning the environment through the console, see **Terminate** your instance.

# References:
https://aws.amazon.com/blogs/infrastructure-and-automation/automate-ansible-playbook-deployment-amazon-ec2-github/
https://aws.amazon.com/blogs/apn/getting-started-with-ansible-and-dynamic-amazon-ec2-inventory-management/
https://docs.ansible.com/ansible/latest/scenario_guides/guide_aws.html
