
# Linux-Server-Configuration-Project

This project is 5th project of Udacity's Full Stack Web Developer Nanodgree and it is about how to secure and set up a Linux server to host a web application by deploy the  item catalog web application.

## IP, Hostname & url
- ip : 18.185.64.1
- port :2200
- url : [http://18.185.64.1/](http://18.185.64.1/).

## Software Installed
* [Ubuntu](https://www.ubuntu.com/download/server) 16.04 LTS, Linux 
* [Amazon Lightsail](https://lightsail.aws.amazon.com). server 
* [PostgreSQL](https://www.postgresql.org/) database server.
* Item catalog project.
# Linux Configuration
 - Get server
- Start new ubuntu linux server instance on Amazon Lightsail
- Make acctount in [Amazon Lightsail](https://lightsail.aws.amazon.com).
- Click on `create instance`.
- Choose `Linux/unix` platform, `OS Only`, and `Ubuntu 16.04 LST`.
- Choose an instance plan (I took the cheapest, $3.5/month).
- Rename your instance or keep it as the default one (I rename it to Aisha).
- Then, click on `Create`
- Wait the instance to `running`.
 - SSH into the server 
- From `account` - `ssh keys` download the default private key
- Move this private key file named LightsailDefaultPrivateKey-*.pem into the local folder ~/.ssh and rename it udacity.pem.`
- Log into the server as the user ubuntu using `ssh -i ~/.ssh/udacity.pem ubuntu@18.185.64.1`
- Log as root using `sudo su -`
- Create a user called grader using `sudo adduser grader` and enter a password
- `sudo nano /etc/sudoers.d/grader` write this command to make file for grader by paste this line `grader  ALL=(ALL:ALL) ALL`
- Then Secure server by Update all currently installed packages:
 `sudo apt-get update`
 `sudo apt-get upgrade`
 `sudo apt-get dist-upgrade`
- Install Finger tool with the command `sudo apt-get install finger`
- Open another terminal and log to folder ~/.ssh
- Write `ssh-keygen -f ~/.ssh/udacity.rsa`
- Enter passphrase 'aisha'
- write cat `~/.ssh/udacity.rsa.pub`, you will get `ssh-rsa`
- Now you stil in the root, go to `cd /home/grader`
- Make new directory called .ssh using the command `mkdir .ssh`
- `touch .ssh/authorized_keys` write this command to store the public key you have it as `ssh-rsa` and edit it using `nano .ssh/authorized_keys` by paste the public key
- Change the permissions of the file by these commands:
`sudo chmod 700 /home/grader/.ssh` 
`sudo chmod 644 /home/grader/.ssh/authorized_keys`
- Change the owner of .ssh from root to grader by `sudo chown -R grader:grader /home/grader/.ssh`
- Now configure the firewall using these commands:
- ` sudo ufw allow 2200/tcp` 
` sudo ufw allow 80/tcp` 
` sudo ufw allow 123/udp` 
` sudo ufw enable` 
- Then restart .ssh by  `sudo service ssh restart`
- Write this command sudo nano /etc/ssh/sshd_config and edit port 22 to 2200 then resatart agian.

# Deploy The Project.
- Begin with install all the required software
``` 
sudo apt-get install apache2
sudo apt-get install libapache2-mod-wsgi python-dev
sudo apt-get install git
```
- Then enable wsgi and restart apache2 
``` 
sudo a2enmod wsgi 
sudo service apache2 restart
``` 
- Now make the user grader the owner and create the catalog directry
``` 
cd /var/www
sudo mkdir catalog
sudo chown -R grader:grader catalog
cd catalog
``` 
- Colne your project `git clone [repository url] catalog`
- Create the .wsgi file by `sudo nano catalog.wsgi` and check the secret key matches with your project secret key
- Rename your project by `mv project.py __init__.py`
- Create virtual environment in /var/www/catalog
``` 
sudo pip install virtualenv
sudo virtualenv venv
source venv/bin/activate
sudo chmod -R 777 venv
``` 
- Install all packages required for Flask application
``` 
sudo apt-get install python-pip
sudo pip install flask
sudo pip install httplib2 oauth2client sqlalchemy psycopg2
sudo pip install requsets
``` 
- Now Add path to `client_secrets.json`  file as  `/var/www/catalog/catalog/client_secrets.json`
- Configure and enable virtual host to run the site using this commaned
`sudo nano /etc/apache2/sites-available/catalog.conf` and paste the follwing:
``` 
<VirtualHost *:80>
    ServerName [Public IP]
    ServerAlias [Hostname]
    ServerAdmin admin@35.167.27.204
    WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
    WSGIProcessGroup catalog
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
- Enable virtual host:`sudo a2ensite catalog.conf`
- DISABLE the default host `a2dissite 000-default.conf`
- Setting up the database
```
sudo apt-get install libpq-dev python-dev
sudo apt-get install postgresql postgresql-contrib
sudo su - postgres
psql
```
- Create a database user and password
```
postgres=# CREATE USER catalog WITH PASSWORD [password];
postgres=# ALTER USER catalog CREATEDB;
postgres=# CREATE DATABASE catalog with OWNER catalog;
postgres=# \c catalog
catalog=# REVOKE ALL ON SCHEMA public FROM public;
catalog=# GRANT ALL ON SCHEMA public TO catalog;
catalog=# \q
$ exit
```
- Go back to grader
- Change the database engine from sqlite://catalog.db to postgresql://username:password@localhost/catalog in database_setup.py , __init__.py and seeder.py
- Finally restart apache2 `sudo service apache2 restart`

 ## Helpful Resources
- [mulligan121](https://github.com/mulligan121)/[udacity-linux-server-configuration](https://github.com/mulligan121/Udacity-Linux-Configuration/blob/master/README.md) 
- [boisalai](https://github.com/boisalai)/[udacity-linux-server-configuration](https://github.com/boisalai/udacity-linux-server-configuration)
- [https://whatismyipaddress.com/ip-hostname](https://whatismyipaddress.com/ip-hostname) For host
 - [Get started on Lightsail](https://classroom.udacity.com/nanodegrees/nd004-connect/parts/226fb92a-d5dc-4d10-add0-c1dabff6ee69/modules/56cf3482-b006-455c-8acd-26b37b6458d2/lessons/046c35ef-5bd2-4b56-83ba-a8143876165e/concepts/c4cbd3f2-9adb-45d4-8eaf-b5fc89cc606e)

 
