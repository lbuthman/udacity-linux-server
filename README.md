# Project Requirements

You will take a baseline installation of a Linux distribution on a virtual machine and prepare it to host your web 
applications, to include installing updates, securing it from a number of attack vectors and installing/configuring
web and database servers.

## Server Info

<p>IP Address: 18.221.61.38</p>
<p>SSH Port: 2200</p>
<p>Application URL: http://18.221.61.38</p>

## Installed Software Summary

Finger
Apache2
Libapache2-Mod-Wsgi
PostgreSQL

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
sudo apt-get install libapache2-mod-wsgi
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

## Third Party Resources
