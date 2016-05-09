# AWS-LAPP-config
LAPP (Linux, Apache, Python, Postgres) stack configuration for an Amazon AWS server

## Access data

The server is hosting https://github.com/OscarDoc/restaurants at:
  * http://52.26.206.3

Info for the reviewer:
  * IP: 52.26.206.3
  * SSH Port: 2200

## Configuration

### How to access the server

Sources: [Udacity](https://www.udacity.com/account#!/development_environment)

Considering the private key is named udacity_key.rsa:

**Linux**:
  * Download private key for the AWS instance. 
  * Move the private key file into the folder ~/.ssh:
    * `$ mv ~/Downloads/udacity_key.rsa ~/.ssh/`
  * Set file rights (only owner can write and read.):
    * `$ chmod 600 ~/.ssh/udacity_key.rsa`
  * SSH into the instance:
    * `$ ssh -i ~/.ssh/udacity_key.rsa root@PUPLIC-IP-ADDRESS`
 
**Windows**:
  * Download private key.
  * Use Puttygen to save the key in .ppk (Putty's) format.
  * Configure Putty to use the public IP of the server and the just created .ppk

### Create a sudoer user

Sources: [Course UD 229 - Configuring Linux Web Servers](https://www.udacity.com/course/configuring-linux-web-servers--ud299)

Logged in as `root`:
  * Create a new user named `grader`:
    * `sudo adduser grader`
    * Set a secret password
  * Give `grader` permission to sudo by editing Ubuntu's sudoers.d file as root:
    * `vi /etc/sudoers.d/grader`
    * Copy in the file: `grader ALL=(ALL) NOPASSWD:ALL`
 
### Update all currently installed packages

Sources: [Course UD 229 - Configuring Linux Web Servers](https://www.udacity.com/course/configuring-linux-web-servers--ud299)

Run:
  * `sudo apt-get update`
  * `sudo apt-get upgrade`
 
The first command will update package references, while the second actually updates packages.

### Change the SSH port from 22 to 2200

Sources: [Ask Ubuntu answer](http://askubuntu.com/a/16661)

  * `sudo vi /etc/ssh/sshd_config`
    * Change Port to 2200
    * Temporalily change PasswordAuthentication from no to yes, so `grader` can access until a key pair is generated.
    * At the end of the file, add `AllowUsers grader`.
  * Restart SSH:
    * `sudo service ssh restart`

**From now on**, your SSH connection has to be configured on **port 2200** and **use `grader`** to log in.

### Configure RSA authorization for SSH

Sources: [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server), [Course UD 229 - Configuring Linux Web Servers](https://www.udacity.com/course/configuring-linux-web-servers--ud299), [GitHub Help](https://help.github.com/articles/generating-an-ssh-key/)

Generate SSH key pairs **on the local machine**; a private key file must be treated as your biggest secret.

**Linux**:
  * Create key filling the data the command asks for:
    * `$ ssh-keygen`
  * Copy the public id to the server:
    * `$ ssh-copy-id grader@PUBLIC-IP-ADDRESS -p2200`
  * Login with the new user:
    * `$ ssh -v grader@PUBLIC-IP-ADDRESS -p2200`
  * Deny again password authentication:
    * `$ sudo vim /etc/ssh/sshd_config`
    * Change PasswordAuthentication back from yes to no.

**Windows**:
  * Create a key pair with Puttygen
  * Access as `grader` with Putty, and from $HOME, run:
    * `$ mkdir .ssh`
    * `$ chmod 700 .ssh`
    * `$ touch .ssh/authorized_keys`
    * `$ chmod 644 .ssh/authorized_keys` 
    * `$ vi .ssh/authorized_keys`
      * Copy in the public key just generated with Puttygen.
  * Deny again password authentication:
    * `$ sudo vim /etc/ssh/sshd_config`
    * Change PasswordAuthentication back from yes to no.
  
### Extra: Deny root's SSH access

Sources: [Ask Ubuntu](http://askubuntu.com/questions/27559/how-do-i-disable-remote-ssh-login-as-root-from-a-server)

  * `$ sudo vi /etc/ssh/sshd_config`
    * Change PermitRootLogin from without-password to no.

**FROM NOW ON ALL SSH ACCESS SHOULD BE DONE AS `grader` WITH THE PRIVATE KEY FILE** 

### Configure the Uncomplicated Firewall (UFW)

Sources: [Course UD 229 - Configuring Linux Web Servers](https://www.udacity.com/course/configuring-linux-web-servers--ud299)

Configure `ufw` to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123):
  * Set up default rules:
    * `$ sudo ufw default deny incoming`
    * `$ sudo ufw default alow outgoing`
  * Allow incoming TCP packets on port 2200 (SSH):
    * `$ sudo ufw allow 2200/tcp`
  * Allow incoming TCP packets on port 80 (HTTP):
    * `$ sudo ufw allow 80/tcp`
  * Allow incoming UDP packets on port 123 (NTP):
    * `$ sudo ufw allow 123/udp`
  * Enable Firewall:
    * `$sudo ufw enable`

### Configure the local timezone to UTC

Sources: [Ubuntu site](https://help.ubuntu.com/community/UbuntuTime#Using_the_Command_Line_.28terminal.29), [Course UD 229 - Configuring Linux Web Servers](https://www.udacity.com/course/configuring-linux-web-servers--ud299)

  * Open the timezone selection dialog:
    * `$ sudo dpkg-reconfigure tzdata`
  * Then chose 'None of the above', then UTC.

### Extra: Setup the ntp daemon

Sources: [Ubuntu site](https://help.ubuntu.com/community/UbuntuTime#Using_the_Command_Line_.28terminal.29)

Install ntp daemon to do regular time syncs:
  * `$ sudo apt-get install ntp`

### Install and configure Apache to serve a Python mod_wsgi application

Sources: [Udacity blog](http://blog.udacity.com/2015/03/step-by-step-guide-install-lamp-linux-apache-mysql-python-ubuntu.html), [Course UD 229 - Configuring Linux Web Servers](https://www.udacity.com/course/configuring-linux-web-servers--ud299), [Ask Ubuntu](http://askubuntu.com/questions/256013/could-not-reliably-determine-the-servers-fully-qualified-domain-name)

  * Install Apache web server:
    * `$ sudo apt-get install apache2`
  * Open a browser and open the public ip address. It should say 'It works!' on the top of the page.
  * Install mod_wsgi for serving Python apps from Apache and the helper package python-setuptools:
    * `$ sudo apt-get install python-setuptools libapache2-mod-wsgi`
  * Restart the Apache server for mod_wsgi to load:
    * `$ sudo service apache2 restart`
  * Get rid of the message __Could not reliably determine the servers's fully qualified domain name__ after restart:
    * `$ sudo vi /etc/apache2/apache2.conf`
    * Add `ServerName localhost` at the end of the file.

### Install and configure PostgreSQL:

Sources: [DigitalOcean[(https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps)

Create a new user named catalog that has limited permissions to the catalog application database
  * Install PostgreSQL: `$ sudo apt-get install postgresql postgresql-contrib`
  * Check that no remote connections are allowed (default): `$ sudo vim /etc/postgresql/9.3/main/pg_hba.conf`
  * Create needed linux user for psql: `$ sudo adduser catalog`
  * Change to default user postgres: `$ sudo su - postgres`
  * Connect to the system: `$ psql`
  * Add postgre user with password and it to create tables:(# stands for the command prompt in psql):
    * `# CREATE USER catalog WITH PASSWORD 'PW-FOR-DB';`
    * `# ALTER USER catalog CREATEDB;`
  * Create database: `# CREATE DATABASE catalog WITH OWNER catalog;`
  * Connect to the database catalog `# \c catalog`
  * Revoke all rights: `# REVOKE ALL ON SCHEMA public FROM public;`
  * Grant only access to the catalog role: `# GRANT ALL ON SCHEMA public TO catalog;`
  * Exit out of PostgreSQl and the postgres user:
    * `# \q`
    * `$ exit`

Do not allow remote connections:
  * Check that no remote connections are allowed (default): `$ sudo vim /etc/postgresql/9.3/main/pg_hba.conf`

### Install git, clone and setup Catalog App project 

Sources: [GitHub](https://help.github.com/articles/set-up-git/#platform-linux), [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps), [StackOverflow](http://stackoverflow.com/questions/14695278/python-packages-not-installing-in-virtualenv-using-pip)

Install git, setting name and username:
  * `$ sudo apt-get install git`
  * `$ git config --global user.name "YOUR NAME"`
  * `$ git config --global user.email "YOUR EMAIL ADDRESS"`

Setup wsgi for Flask:
  * Install required packages: 
    * `$ sudo apt-get install libapache2-mod-wsgi python-dev`
  * Enable mod_wsgi (if not already enabled):
    * `$ sudo a2enmod wsgi`
  * Create a structure for the Flask app:
    * `$ cd /var/www`
    * `$ sudo mkdir catalog`
    * `$ cd catalog`
    * `$ sudo mkdir catalog`
    * `$ cd catalog`
    * `$ sudo mkdir static templates`

Install Flask:
  * Install  PIP:
    * `$ sudo apt-get install python-pip`
  * Install virtualenv:
    * `$ sudo pip install virtualenv`
  * Set virtual environment to name 'venv':
    * `$ sudo virtualenv venv`
  * Enable all permissions for the new virtual environment (no sudo should be used within):
    * `$ sudo chmod -R 777 venv`
  * Activate the virtual environment:
    * `$ source venv/bin/activate`
  * Install Flask inside the virtual environment:
    * `$ pip install Flask`
  * Deactivate the environment:
    * `$ deactivate`

Configure a New Virtual Host
  * Create a virtual host config file
    * `$ sudo nano /etc/apache2/sites-available/catalog.conf`
  * Paste in the following lines of code and change names and addresses regarding your application:
  ```
<VirtualHost *:80>
      ServerName PUBLIC-IP-ADDRESS
      ServerAdmin admin@PUBLIC-IP-ADDRESS
      WSGIScriptAlias / /var/www/catalog/catalog.wsgi
      <Directory /var/www/catalog/catalog/>
          Order allow,deny
          Allow from all
      </Directory>
      Alias /static /var/www/catalog/catalog/static
      <Directory /var/www/catalog/catalog/static/>
          Order allow,deny
          Allow from all
      </Directory>
      ErrorLog ${APACHE_LOG_DIR}/error.log
      LogLevel warn
      CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>
  ```
  
  * Enable the virtual host:
    * `$ sudo a2ensite catalog`

Create WSGI file:
  * `$ cd /var/www/catalog`
  * `$ sudo vim catalog.wsgi`
  * Paste in the following lines of code:
  ```
    #!/usr/bin/python
    import sys
    import logging
    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0,"/var/www/catalog/catalog/venv")
    from catalog import app as application
  ```
  
  * Restart Apache:
    `$ sudo service apache2 restart`


Open the database setup file: `$ sudo vim database_setup.py`
Change the line starting with "engine" to (fill in a password):
python engine = create_engine('postgresql://catalog:PW-FOR-DB@localhost/catalog')
Change the same line in application.py respectively
Rename application.py:
$ mv application.py __init__.py
Create postgreSQL database schema:
$ python database_setup.py

Your README.md file should include all of the following:
i. The IP address and SSH port so your server can be accessed by the reviewer.
ii. The complete URL to your hosted web application.
iii. A summary of software you installed and configuration changes made.
iv. A list of any third-party resources you made use of to complete this project.



