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

## Configuration Steps

### Update Available Package Lists

```sudo apt-get update
sudo apt-get upgrade
sudo apt-get autoremove
```

### Install Software

```sudo apt-get install finger```

### Create a new user

```sudo adduser grader```

### Grant new user sudo permission

```sudo chmod 755 /etc/sudoers.d
sudo touch /etc/sudoers.d/grader
sudo chmod 777 /etc/sudoers.d/grader
sudo echo "grader ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/grader
sudo chmod 755 /etc/sudoers.d/grader```

## Third Party Resources
