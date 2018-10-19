# Deploying Musical Instrument Project onto Linux
This project demonstrates a Flask application being deployed to a Amazon Lightsail. This document will highlight the necessary information and will provide a summary of the software installed and updated, and configuration steps made in the setup of this server. 

# IP Address
34.211.88.8

# SSH Port
Port 22

#URL 
http://34.211.88.8/instrument/

## Prerequisites
Access to a browser to view the listed IP and a terminal to ssh into the server.

## Installation and Updates Performed
* `sudo apt-get update` to update Ubuntu
* `sudo apt-get upgrade` to upgrade Ubuntu
* `sudo adduser grader` to allow super user privileges
* `sudo visudo` and added `grader  ALL=(ALL:ALL) ALL` to enable grader to ssh into system
* modified the sshd_config file to allow Port 2200 to be the active ssh port and disallowed Root login via `PermitRootLogin no`
* `sudo dpkg-reconfigure tzdata` to configure the timezone to UTC
* `sudo apt-get install libapache2-mod-wsgi python-dev` to install Mod_wsgi
* `sudo a2enmod wsgi` to enable Mod_wsgi
* `sudo apt-get install python-pip` to instal pip
* `sudo pip install virtualenv` to install virtualenv
* `sudo virtualenv venv` to setup virtual environment
* `source venv/bin/activate` to activate virtual environment
* `sudo pip install Flask` to install Flask
* `sudo nano /etc/apache2/sites-available/FlaskApp.conf` to configure the virtual host with the appropriate information:
```
<VirtualHost *:80>
ServerName 34.211.88.8
ServerAdmin admin@18.236.156.34.com
WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
<Directory /var/www/FlaskApp/FlaskApp/>
Order allow,deny
Allow from all
</Directory>
Alias /static /var/www/FlaskApp/FlaskApp/static
<Directory /var/www/FlaskApp/FlaskApp/static/>
Order allow,deny
Allow from all
</Directory>
ErrorLog ${APACHE_LOG_DIR}/error.log
LogLevel warn
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
* `sudo nano flaskapp.wsgi` to create a wgsi file:
```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/FlaskApp/")

from FlaskApp import app as application
application.secret_key = 'super_secret_key'
```
* `sudo git clone httpshttps://github.com/smellyRose/music-inst-proj` within `var/www/FlaskApp/FlaskApp` to install the instrument project stored on GitHub
* `sudo nano mv musical_instruments_project.py __ init __` to change file name of main driver
* `sudo apt-get install postgresql` to install postgresql
* `sudo su - postgres` and `psql` to login as a postgres user
* `CREATE USER musicdb WITH PASSWORD 'musicdb';` to create new user
* `ALTER USER musicdb CREATEDB;` to allow user to create database
* `CREATE DATABASE musicdb WITH OWNER musicdb;` to create a new database
* `musicdb=# REVOKE ALL ON SCHEMA public FROM public;`to disallow outside changes
* `catalog=# GRANT ALL ON SCHEMA public TO musicdb;` to allow viewing access
* engine creations within python files need to be edited to `engine = create_engine('postgresql://musicdb:musicdb@localhost/musicdb')`
* Files needed to be updated to conform to new database location:
- __ init __.py   
- database_setup.py
- instrument_database_setup_longnames.py

* `sudo python database_setup.py` and `instrument_database_setup_longnames.py` to update database
* `sudo service apache2 restart` to restart apache
#### Built With
Ubuntu, Python 2.7 and deplyoed on Amazon Lightsail

#### Sources

[What is the right file permission for a .pem file to SSH and SCP](https://unix.stackexchange.com/questions/115838/what-is-the-right-file-permission-for-a-pem-file-to-ssh-and-scp)
[How To Deploy a Flask Application on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
[Deploying Flask and Postgre Using Apache and WSGI](https://knowledge.udacity.com/questions/10055)

#### Authors
Joey Berger