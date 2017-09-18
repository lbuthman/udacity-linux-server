# Project Requirements

You will take a baseline installation of a Linux distribution on a virtual machine and prepare it to host your web 
applications, to include installing updates, securing it from a number of attack vectors and installing/configuring
web and database servers.

## Server Info

<p>IP Address: 18.221.61.38</p>
<p>SSH Port: 2200</p>
<p>Application URL: http://18.221.61.38</p>

## Installed Software Summary

Ubuntu
Finger

## Third Party Resources

Apache2
Libapache2-Mod-Wsgi
PostgreSQL
Flask
SQL Alchemy
HttpLib2
OAuth2Client
Requests

## Configuration Steps

### Update Available Package Lists

```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get autoremove
```

### Install Software

```
sudo apt-get install finger
```

### Create a new user

```
sudo adduser grader
```

### Grant new user sudo permission

```
sudo chmod 755 /etc/sudoers.d
sudo touch /etc/sudoers.d/grader
sudo chmod 777 /etc/sudoers.d/grader
sudo echo "grader ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/grader
sudo chmod 755 /etc/sudoers.d/grader
```

### Generate and Setup Keys for SSH Authentication

#### Generate Public/Private Keys
```
ssh-keygen
```

#### Setup public key on server
```
su grader
mkdir ~/.ssh
touch ~/.ssh/authorized_keys
```

#### Copy contents of key.pub file on Local Machine
  
#### Paste contents into authorized_keys file
```
nano ~/.ssh/authorized_keys
```

#### Set permissions for folder and file
```
chmod 700 ~/.ssh
chmod 644 ~/.ssh/authorized_keys
```

### Ensure Key Based Authentication and Root Can't Login
```
sudo nano /etc/ssh/sshd_config
Port 2200
PermitRootLogin no
Password Authentication no
```

### Restart SSH
```
sudo service ssh restart
```

### Enable Custom Rule On LightSail
Networking tab -> Custom TCP 2200

### Configure Ports in UFW (Uncomplicated Firewall)

#### Default incoming policy
```
sudo ufw default deny incoming
```

#### Default outgoing policy
```
sudo ufw default allow outgoing
```

#### Listen on Port 2200 for SSH
```
sudo ufw allow 2200/tcp
```

#### Listen on Port 80 for HTTP
```
sudo ufw allow www
```

#### Listen on Port 123 for NTP (Network Time Protocol)
```
sudo ufw allow ntp
```

#### Enable the Firewall
```
sudo ufw enable
```

#### Verify Firewall Setup
```
sudo ufw status
```

### Configure UTC time
```
sudo dpkg-reconfigure tzdata
```

### Install Apache Web Server
```
sudo apt-get install apache2
```

Verify by going to IP address and seeing Apache2 Ubuntu Default Page

### Install mod_wsgi
```
sudo apt-get install libapache2-mod-wsgi-py3
```

### Install PostgreSQL and configure

#### Install
```
sudo apt-get install postgresql
```

#### Login as Postgres User
```
sudo -i -u postgres
```

#### Create catalog User with limited access
```
createuser --interactive -P
```

Answer no to all questions.

#### Create Database 
```
psql
CREATE DATABASE catalog;
\q
```

#### Switch back to Grader
```
exit
```

### Install and Configure Git

#### Install (actually installed already, sweet!)
```
sudo apt-get install git
```

#### Inititalize project repo
```
sudo mkdir /var/www/catalog
sudo chown grader:grader /var/www/catalog
cd /var/www/catalog
sudo git clone https://github.com/lbuthman/udacity-catalog.git
sudo mv /var/www/catalog/udacity-catalog /var/www/catalog/catalog
```

#### Protect .git from Public
```
sudo chmod 700 /var/www/catalog/catalog/.git
```

### Install Project Dependencies
```
sudo apt-get -qqy install python-flask
sudo apt-get -qqy install python-sqlalchemy
sudo apt-get -qqy install python-pip
sudo pip install --upgrade pip
sudo pip install oauth2client
sudo pip install httplib2
sudo pip install requests
```

### Setup Flask Project with Database

#### Rename project.py
```
sudo mv project.py __init.py__
```

#### Edit __init.py__
```
sudo nano __init.py__
```
add absolute path to client secrets
engine = create_engine('postgresql://catalog:catalog@localhost/catalog')

#### Edit database_setup.py and lotsofexercises.py
```
sudo nano database_setup.py
sudo nano lotsofexercises.py
```
engine = create_engine('postgresql://catalog:catalog@localhost/catalog')

#### Run Database Setup Commands
```
sudo python database_setup.py
sudo python lotsofexercises.py
```

#### Sanity Check Database Setup
```
sudo -i -u postgres
postgres catalog
\dt
select * from category;
select * from exercise;
\q
exit
```

### Configure Entry Point and Virtual Host

#### Create wsgi File
```
sudo nano /var/www/catalog/catalog.wsgi
```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from catalog import app as application

#### Create Host File
```
sudo nano /etc/apache2/sites-available/catalog.conf
```
<VirtualHost *:80>
                ServerName exercisedatabase.com
                ServerAdmin admin@mywebsite.com
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

#### Configure Apache to Handle Requests
```
/etc/apache2/sites-enabled/000-default.conf
```
Append following to the end of the file
`WSGIScriptAlias / /var/www/catalog/catalog/catalog.wsgi`

### Restart Service
```
sudo service apache2 restart
```
