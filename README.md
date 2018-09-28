# Linux Server Configuration
This is the third project for "Full Stack Web Developer Nanodegree" on Udacity.

In this project, i will take a baseline installation of a Linux server and prepare it to host your web applications. I will secure my server from a number of attack vectors, install and configure a database server, and deploy one of your existing web applications onto it.

## 1. Instructions for SSH to access the instance
1. Download the private key from (Account--> SSH key)
2. Move the private key file into the folder `~/.ssh` (you will find it in your home directory). You can just type the following command in your terminal:
	```mv ~/Downloads/udacity_key.rsa ~/.ssh/```
Note: Downloads refers to the directory that the private key file downloaded in.
3. Open your terminal and type :
	```chmod 600 ~/.ssh/udacity_key.rsa```
4. Type in your terminal to access ubunto server
	```ssh -i ~/.ssh/udacity_key.rsa root@52.59.188.225```
5. Information

	Public IP Address (52.59.188.225)
  
## 2. Update all currently installed packages
1. To check for updates type in terminal:
  ```sudo apt-get update```
2. To upgrade type in terminal:
  ```sudo apt-get upgrade```
3. To remove packages the no longer required type in terminal:
  ```sudo apt-get autoremove```
4. To install finger type in terminal:
  ```sudo apt-get install finger```
  
## 3. Change the SSH port from 22 to 2200
1. Use `sudo nano /etc/ssh/sshd_config` and then change Port 22 to Port 2200 , save & exit.
2. Reload SSH using `sudo service ssh restart`

## 4. Configure the Uncomplicated Firewall (UFW) 
Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for
1. SSH (port 2200) type:
  ```sudo ufw allow 2200/tcp```
2. HTTP (port 80) type:
  ```sudo ufw allow 80/tcp```
3. NTP (port 123) type:
  ```sudo ufw allow 123/udp```
4. ```sudo ufw enable```
## 5. Create a new user account named grader
1. ```sudu adduser grader```
then type your password and full name

## 6. Give grader the permission to sudo.
1. `touch /etc/sudoers.d/grader`
2. `sudo vim /etc/sudoers.d/grader`, type `grader ALL=(ALL:ALL) ALL`, save and exit by typing `Esc` + `:` + `x!` and press `enter`

## 7. Create an SSH key pair for grader
1. Generate keys on local machine using`ssh-keygen` , then save the private key in `~/.ssh` on your local machine
2. Go on your virtual machine and type:
```
su grader
mkdir .ssh
touch .ssh/authorized_keys
```
3. On your local machine type:
```
cat .ssh/linuxcourse.pub
```
Then copy the key
4. Go back to your virtual machine and type:
`nano .ssh/authorized_keys`
Then past the key and save,
```
chmod 700 .ssh
chmod 644 .ssh/authorized_keys
 ```
## 8. Configure the local timezone to UTC
`sudo dpkg-reconfigure tzdata`

## 9. Install and configure Apache to serve a Python mod_wsgi application
1. Install Apache `sudo apt-get install apache2`
2. Install mod_wsgi `sudo apt-get install python-setuptools libapache2-mod-wsgi`
3. Restart Apache `sudo service apache2 restart`

## 10. Install and configure PostgreSQL
1. Install PostgreSQL `sudo apt-get install postgresql`
2. Login as user "postgres" `sudo su  postgres`
3. Open postgreSQL shell `psql`
4. Create a new database named catalog  and create a new user named catalog in postgreSQL shell
	
	```
	CREATE DATABASE catalog;
	CREATE USER catalog;
	```
5. Set a password for user catalog
	
	```
	ALTER ROLE catalog WITH PASSWORD 'password';
	```
6. Give user "catalog" permission to "catalog" application database
	
	```
	GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
	```
7. Quit postgreSQL `\q`
8. Exit from user "postgres" 
	
	```
	exit
	```
  
## 11. Install git
1. Install Git type:
`sudo apt-get install git`
2. to move to the /var/www directory type:
`cd /var/www` 
3. Create the application directory by typing:
`sudo mkdir FlaskApp`
4. Move inside this directory type:
`cd FlaskApp`
5. Clone the Catalog App to the virtual machine by typing:
`git clone https://github.com/alikhaled97/FlaskApp.git`
6. Rename `catalog.py` to `__init__.py` type:
`sudo mv catalog.py __init__.py`
7. Edit `database_setup.py`, `catalog.py` and `someofcategories.py` by changing `engine = create_engine('sqlite:///catalog.db')` to `engine = create_engine('postgresql://catalog:password@localhost/catalog')`
8. Install pip type:
`sudo apt-get install python-pip`
9. Install psycopg2:
`sudo apt-get -qqy install postgresql python-psycopg2`
10. Create database:
`sudo python database_setup.py`

## 12. Configure and Enable a New Virtual Host
1. Create FlaskApp.conf to edit: `sudo nano /etc/apache2/sites-available/FlaskApp.conf`
2. Add the following lines of code to the file to configure the virtual host. 
	
	```
	<VirtualHost *:80>
		ServerName 52.59.188.225
		ServerAdmin ali97@hotmail.com
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
3. Enable the virtual host with the following command: `sudo a2ensite FlaskApp`

## Create the .wsgi File
1. Create the .wsgi File in /var/www/FlaskApp: 
	
	```
	cd /var/www/FlaskApp
	sudo nano flaskapp.wsgi 
	```
2. Add the following lines of code to the flaskapp.wsgi:
	
	```
	#!/usr/bin/python
	import sys
	import logging
	logging.basicConfig(stream=sys.stderr)
	sys.path.insert(0,"/var/www/FlaskApp/")

	from FlaskApp import app as application
	application.secret_key = 'Add your secret key'
	```

## Restart Apache
`sudo service apache2 restart `



## Contacts

- [Facebook](https://www.facebook.com/ali.khaled.71465)
- E-mail: engalikhaled@hotmail.com


## References:
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

