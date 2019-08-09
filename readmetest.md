# Linux Server Configuration Project

The Linux Server Configuration project was the last project for the Udacity Full Stack Nanodegree program. The goal of this project was to create a web application that would use Item-Catalog application developed earlier in the course. The directions below will outline how to create a Lnux server and deploy the Item-Catalog application. 

You can visit http://54.71.206.55.xip.io

## Step by Step Walkthrough
### Setup your server in Amazon Lightsail
**1.** Create a new Ubuntu Linux Server instance using [Amazon Lightsail](https://lightsail.aws.amazon.com/).
* Create an Amazon Web Services account, or log into an existing account
* Select Create an instance 
* Select the Linux/Unix Platform 
* Select the 18.04 LTS Ubuntu BluePrint 
* Select an Instance Plan, the cheapest option will suffice for this program
* Name the Instance and select "Create Instance"
* Select your instance, the Public IP adresss will be displayed this is the IP address that will be used to connect the web application

**2.** Get default key that will be used to ssh into the server
* Select the Account Page link in blue on the Connect tab ("You configured this instance to use default (us-west-2) key pair. You can downoad your default private key from the Account page)
* Download the default key
* Copy the key to your C:\\users\\[your user]\\.ssh and rename it to Default.pem
* Launch a command prompt through Git Bash
* Adjust the access level to the default key by typing in the following command: ``` chmod 600 ~/.ssh/Default.pem ```

**3.** Connect to Server
* Type in the following command into the prompt: 
``` ssh -i ~/.ssh/Default.pem ubuntu@54.71.206.55 ```

### Secure the server

**4.** Update all system packages to most recent versions
* In the terminal after connecting with ubuntu run the following commands:
```
sudo apt-get update
sudo apt-get upgrade
```

**5.** Change the SSH port to 2200 which is not the default 22
* Update the sshd_config file
	- Type in ``` sudo nano /etc/ssh/sshd_config ```
	- Uncomment Port 22 and change 22 to 2200
	- Uncomment PermitRootLogin and set it to not
	- To Save: Hit ``` CTRL+X ``` and select Y
	- Every change to the ssh config file requires a restart to the ssh connection. Do this by typing ``` sudo service ssh restart ```

**6.** Configure the Firewall to allow ssh port 2200, udp port 123, and http connections
* Run the following commands:
```
sudo ufw status
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200/tcp
sudo ufw allow www
sudo ufw allow 123/udp
sudo ufw deny 22
sudo ufw enable
```
*Select y
*Select exit

* To verify the firewall status type in: ``` sudo ufw status ```
* Adjust the Lightsail Server
	- Navigate to your server on [Amazon Lightsail](https://lightsail.aws.amazon.com/).
	- Allow ports 2200 (TCP), 80 (TCP) and 123 (UDP)
	- Remove port 22 (TCP)
* Exit out of the ubuntu terminal and reconnect by typing in ``` ssh -i ~/.ssh/Default.pem -p 2200 ubuntu@54.71.206.55 ```

### Create grader user and give appropriate access

**7.** Create grader user
* In ubuntu terminal run the following commands:
```
sudo adduser grader
```
* Set a password
8. Give grader user sudo access
* Run the following command:
```
sudo visudo
```
* Under the line ```root ALL=(ALL:ALL) ALL```, add ```grader ALL=(ALL:ALL) ALL```.
* Save and exit using ```CTRL+X``` and select ```Y``` to save changes. 

**9.** Create SSH key for grader
* Navigate to the .ssh folder
* Run the following commands:
```
 ssh-keygen -f ~/.ssh/grader_key.rsa
 mv grader_key.rsa.pub grader_key.pub
 cat ~/.ssh/grader_key.pub
 ```
 * Copy the contents of gradery_key.pub, will need to make a key in the ssh terminal
 * Run the following commands:
 ```
 cd /home/grader/
 sudo mkdir .ssh
 sudo nano /home/grader/.ssh/authorized_keys
 ```
 * Paste the grader_key.pub contents
 * CTRL+X and Y to save contents

**10.** Adjust the access rights to the authorized_keys
 * Send the following commands:
 ```
 sudo chmod 700 /home/grader/.ssh
 sudo chmod 644 /home/grader/.ssh/authorized_keys
 sudo chown -R grader:grader /home/grader/.ssh
 sudo service ssh restart
 ```
 * Exit out of the ubuntu terminal and reconnect into the grader terminal by typing in: 
 ```
 ssh -i ~/.ssh/grader_key.rsa -p 2200 grader@54.71.206.55
 ```
 ### Prepare to deploy project to the web server

**11.** Install python
 * python3 comes default with the application to install python 2 run the following command:
 ```
 sudo apt install python
 ```
 
**12.** Install Apache
 * While logged into the grader terminal run the command: 
 ```
 sudo apt-get install apache2
 ```
 * Verify that apache was installed correctly by typing in 54.71.206.55 in a web browser

**13.** Install the python2 WSGI package that will allow python to communicate to the web server
 * Run the commands
 ```
 sudo apt-get install libapache2-mod-wsgi
 sudo a2enmod wsgi
 sudo service apache2 start
 ```

**14.** Configure application to read the correct wsgi file
 * Edit the default config file by running the following command:
 ```
 sudo nano /etc/apache2/sites-enabled/000-default.conf
 ```
 * Add the following lines above the ServerAdmin line
	- ServerName 54.71.206.55
	- ServerAlias ec2-54-71-206-55.us-west-2.compute.amazonaws.com
 * Add the following line before the closing </VirtualHost> tag
	- WSGIScriptAlias / /var/www/html/myapp.wsgi
 * CTRL+X and Y to save and exit

**15.** Install Git
 ```
 sudo apt-get install git
 ```

**16.** Install Postgres
 * Type in ``` sudo apt-get install postgresql ```

**17.** Log into postgres and create a database and grant a user access
 * In grader terminal type in: 
 ```
 sudo -u postgres psql
 create database restaurantdb
 create role catalog_user with password 'pw';
 \c restaurantdb
 grant all privileges on database restaurandb to catalog_user;
 ```
 * exit by ``` \q ```

**18.** Install pip for python2
 * sudo apt-get python-pip

**19.** Install all the needed packages:
* Type in the following commands:
```
sudo pip install sqlalchemy
sudo pip install httplib2
sudo apt-get install python-psycopg2
sudo apt-get install libpq-dev
sudo pip install flask
sudo pip install flask-httpauth
sudo pip install flask-sqlalchemy
sudo pip install requests
sudo pip install --upgrade oauth2client
sudo pip install psycopg2
```

### Connect Item-Catalog Project

**20.** Clone and setup my Item-Catalog project
* Log into the grader terminal and run the following commands:
```
cd /var/www/html/
sudo git clone https://github.com/CPinnkathok/Item-Catalog.git
sudo chown -R grader:grader html/
```

**21.** Edit the project files to be able to communicate to the web server
* Database_setup.py
	- replace engine = create_engine('sqlite:///restaurantmenu.db') with engine = create_engine('postgresql://catalog_user:pw@localhost/restaurantdb')
	- CTRL+X and Y to save and exit 
* lotsofmenus.py
	- replace engine = create_engine('sqlite:///restaurantmenu.db') with engine = create_engine('postgresql://catalog_user:pw@localhost/restaurantdb')
	- CTRL+X and Y to save and exit 
* project.py
	- replace engine = create_engine('sqlite:///restaurantmenu.db') with engine = create_engine('postgresql://catalog_user:pw@localhost/restaurantdb')
	- replace  open('client_secret.json', 'r').read())['web']['client_id'] with open('/var/www/html/client_secret.json', 'r').read())['web']['client_id']
	- replace oauth_flow = flow_from_clientsecrets('client_secret.json', scope='') with oauth_flow = flow_from_clientsecrets('/var/www/html/client_secret.json', scope='')
	- Delete the following lines:
    app.secret_key = 'supper secret key'
    app.run(debug=True, host='0.0.0.0', port=5000, threaded=False)
	- in if __name__ == '__main__': add app.run()
	- CTRL+X and Y to save and exit 
* myapp.wsgi
	- delete the existing file: ''' sudo rm /var/www/html/myapp.wsgi '''
	- write a new myapp.wsgi file: ''' sudo nano /var/www/html/myapp.wsgi '''
	- In the text editor past the following:
#!/usr/bin/env python

import sys
sys.path.insert(0, "var/www/html")

from project import app as application

application.secret_key = 'super_secret_key'

* CTRL+X and Y to save and exit

**22.** Configure and enable a new virtual host
*  type in ``` sudo nano /etc/apache2/sites-enabled/000-default.conf ```
* After the WSGIScriptAlias line paste the following:
        <Directory /var/www/html/static/>
                Order allow,deny
                Allow from all
        </Directory>
        <Directory /var/www/html/templates>
                Order allow,deny
                Allow from all
        </Directory>
* CTRL+X and y to save and exit

**23.** Setup the restaurant database
* Run the following commands:
```
cd /var/www/html
sudo python database_setup.py
```
* Adjust the id column to the serial type
	- Type the following commands:
```
sudo -u postgres psql
\c restaurantdb
alter table menu_item drop column id cascade;
alter table restaurant drop column id cascade;
alter table menu_item add column id serial primary key;
alter table restaurant add column id serial primary key;
\q
```
* Add the initial dataset by: ``` sudo python lotsofmenus.py ```

### Update Google Credentials

**24.** Update Client Secret
* In [Google Console Developers Page](https://console.developers.google.com/apis/credentials) Update the API Credentials for the client_secret key
* Go to OAuth Consent Screen and add the following the Authorized domains:
	- ec2-54-71-206-55.us-west-2.compute.amazonaws.com
	- xip.io
	- Save
* Go to the credentials tab and select the correct client id
* Add the following to the Authorized JavaScript Origins:
	- http://54.71.206.55.xip.io
	- http://ec2-54-71-206-55.us-west-2.compute.amazonaws.com
* Add the following the Authorized redirect URIs
	- http://54.71.206.55.xip.io/login
	- http://54.71.206.55.xip.io/gconnect
* Save and download the key
* Copy the contents of the key and connect to the grader ssh terminal
* ``` sudo nano /var/www/html/client_secret.json ```
* paste the key
* CTRL+X and y to save and exit

### Run the application
* In the grader terminal: ``` sudo service apache2 restart ```
* In the web browser go to http://54.71.206.55.xip.io
* You will see the list of restaurant items created by the lotsofmenus.py script, you can now login with the authorized google user to edit the exiting restaurants or add new ones

### Authors
* Christine Pinnkathok
### Acknowledgments
* I would like to thank the Udacity Course, Fullstack Web Development, as well as all the students who contributed to the knowledge center and slack channels. Tim Nelson is an amazing mentor and helped me work through a lot of difficult problems.
* Special thanks to [kcalata](https://github.com/kcalata/Linux-Server-Configuration/blob/master/README.md) for his detailed README
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 

 
 