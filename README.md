# AWS-LAPP-config
LAPP (Linux, Apache, Python, Postgres) stack configuration for an Amazon AWS server

## Access data

The server is hosting https://github.com/OscarDoc/restaurants at:
  * http://52.26.206.3

Info for the reviewer:
  * IP: 52.26.206.3
  * SSH Port: 2200

## Configuration

### How to acess the server

[Udacity](https://www.udacity.com/account#!/development_environment)

Considering the private key is named udacity_key.rsa:

**Linux**:
  * Download private key for the AWS instance. 
  * Move the private key file into the folder ~/.ssh:
    * `$ mv ~/Downloads/udacity_key.rsa ~/.ssh/`
  * Set file rights (only owner can write and read.):
    * `$ chmod 600 ~/.ssh/udacity_key.rsa`
  * SSH into the instance:
    * `$ ssh -i ~/.ssh/udacity_key.rsa root@PUPLIC-IP-ADDRESS`
 
Windows:
  * Download private key.
  * Use Puttygen to save the key in .ppk (Putty's) format.
  * Configure Putty to use the public IP of the server and the just created .ppk

### Create a sudoer user

[Course UD 229 - Configuring Linux Web Servers](https://www.udacity.com/course/configuring-linux-web-servers--ud299)

Logged in as `root`:
  * Create a new user named `grader`:
    * `sudo adduser grader`
    * Set a secret password
  * Give `grader` permission to sudo by editing Ubuntu's sudoers.d file as root:
    * `vi /etc/sudoers.d/grader`
    * Copy in the file: `grader ALL=(ALL) NOPASSWD:ALL`
 
### Update all currently installed packages

[Course UD 229 - Configuring Linux Web Servers](https://www.udacity.com/course/configuring-linux-web-servers--ud299)

Run:
  * `sudo apt-get update`
  * `sudo apt-get upgrade`
 
The first command will update package references, while the second actually updates packages.

### Change the SSH port from 22 to 2200

[Ask Ubuntu answer](http://askubuntu.com/a/16661)

  * `sudo vi /etc/ssh/sshd_config`
    * Change Port to 2200
    * Temporalily change PasswordAuthentication from no to yes, so `grader` can access until a key pair is generated.
    * At the end of the file, add `AllowUsers grader`.
  * Restart SSH:
    * `sudo service ssh restart`

**From now on**, your SSH connection has to be configured on **port 2200** and **use `grader`** to log in.

### Extra: Deny root's SSH

  * `$ sudo vi /etc/ssh/sshd_config`
    * Change PermitRootLogin from without-password to no.


### Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)

### Configure the local timezone to UTC

### Install and configure Apache to serve a Python mod_wsgi application

### Install and configure PostgreSQL:

### Do not allow remote connections

### Create a new user named catalog that has limited permissions to your catalog application database

### Install git, clone and setup your Catalog App project (from your GitHub repository from earlier in the Nanodegree program) so that it functions correctly when visiting your server’s IP address in a browser. Remember to set this up appropriately so that your .git directory is not publicly accessible via a browser!


Your README.md file should include all of the following:
i. The IP address and SSH port so your server can be accessed by the reviewer.
ii. The complete URL to your hosted web application.
iii. A summary of software you installed and configuration changes made.
iv. A list of any third-party resources you made use of to complete this project.


