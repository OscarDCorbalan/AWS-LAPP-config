# AWS-LAPP-config
LAPP (Linux, Apache, Python, Postgres) stack configuration for an Amazon AWS server

## Server data

The server is hosting https://github.com/OscarDoc/restaurants at:
  * http://52.26.206.3

Info for the reviewer:
  * IP: 52.26.206.3
  * SSH Port: 2200

## Configuration

### Server access:

[Udacity](https://www.udacity.com/account#!/development_environment)

Considering the private key is named udacity_key.rsa:

Linux:
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


Launch your Virtual Machine with your Udacity account. Please note that upon graduation from the program your free Amazon AWS instance will no longer be available.
Follow the instructions provided to SSH into your server
Create a new user named grader
Give the grader the permission to sudo
Update all currently installed packages
Change the SSH port from 22 to 2200
Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
Configure the local timezone to UTC
Install and configure Apache to serve a Python mod_wsgi application
Install and configure PostgreSQL:
Do not allow remote connections
Create a new user named catalog that has limited permissions to your catalog application database
Install git, clone and setup your Catalog App project (from your GitHub repository from earlier in the Nanodegree program) so that it functions correctly when visiting your server’s IP address in a browser. Remember to set this up appropriately so that your .git directory is not publicly accessible via a browser!


Your README.md file should include all of the following:
i. The IP address and SSH port so your server can be accessed by the reviewer.
ii. The complete URL to your hosted web application.
iii. A summary of software you installed and configuration changes made.
iv. A list of any third-party resources you made use of to complete this project.


