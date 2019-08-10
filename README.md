# Item Catalog Project

This code will allow the user to create, edit and delete Restaurants and restaurant menu items from a database

## Installing the Vagrant

You'll use a virtual machine (VM) to run a web server and a web app that uses it. The VM is a Linux system that runs on top of your own machine.  You can share files easily between your computer and the VM.

We're using the Vagrant software to configure and manage the VM. Here are the tools you'll need to install to get it running:



If you don't already have Git installed, [download Git from git-scm.com.](http://git-scm.com/downloads) Install the version for your operating system.

On Windows, Git will provide you with a Unix-style terminal and shell (Git Bash).  
(On Mac or Linux systems you can use the regular terminal program.)

You will need Git to install the configuration for the VM. If you'd like to learn more about Git, [take a look at our course about Git and Github](http://www.udacity.com/course/ud775).

### VirtualBox

VirtualBox is the software that actually runs the VM. [You can download it from virtualbox.org, here.](https://www.virtualbox.org/wiki/Downloads)  Install the *platform package* for your operating system.  You do not need the extension pack or the SDK. You do not need to launch VirtualBox after installing it.

**Ubuntu 14.04 Note:** If you are running Ubuntu 14.04, install VirtualBox using the Ubuntu Software Center, not the virtualbox.org web site. Due to a [reported bug](http://ubuntuforums.org/showthread.php?t=2227131), installing VirtualBox from the site may uninstall other software you need.

### Vagrant

Vagrant is the software that configures the VM and lets you share files between your host computer and the VM's filesystem.  [You can download it from vagrantup.com.](https://www.vagrantup.com/downloads) Install the version for your operating system.

**Windows Note:** The Installer may ask you to grant network permissions to Vagrant or make a firewall exception. Be sure to allow this.

### Fetch the Source Code and VM Configuration

**Windows:** Use the Git Bash program (installed with Git) to get a Unix-style terminal.  
**Other systems:** Use your favorite terminal program.

From the terminal, run:

    ```
	https://github.com/CPinnkathok/Item-Catalog.git Item-Catalog
	```
	
This will give you a directory named **Item-Catalog** complete with the source code for the flask application, a vagrantfile, and a bootstrap.sh file for installing all of the necessary tools. 

### Authorization
* You will need to create a client_id in order to log into the Restaurant App through the OAuth Authorization service provided by google  
	- Go to: **https://console.developers.google.com**  
	- Create a new project under a verified google email  
	- Select the project and navigate to the Credentials option under APIs & Services  
	-Go to OAuth Consent screen and name your application and press save  
	- Go to the credentials tab and create a new set of OAuth2.0 Credentials for a web application  
	- Set the Authorized JavaScript Origins to:
		- **http://localhost:5000**  
	- Set the Authorized Redirect URI:
		- **http://localhost:5000/login**  

	- Save the credentials and download the key  
	- Rename the key to client_secret and copy it to the same folder as the database_setup.py and project.py live  

### Required Modules
* This code uses python2  
* The following modules are also needed: Flask, sqlalchemy, oauth2client, and httplib2  
* The requirements.txt file includes all the versions used when developing the application  
* You can run ``` pip install -r requirements.txt ``` to install the required modules  

## Installing

### Run the virtual machine!

Using the terminal, change directory to Item-Catalog ``` cd Item-Catalog ```, then type ``` vagrant up ``` to launch your virtual machine.


### Running the Restaurant Menu App
* Once it is up and running, type ``` vagrant ssh ``` This will log your terminal into the virtual machine, and you'll get a Linux shell prompt. 
* When you want to log out, type ``` exit ``` at the shell prompt.  
* To turn the virtual machine off (without deleting anything), type ``` vagrant halt ```. If you do this, you'll need to run ``` vagrant up ``` again before you can log into it.
* Now that you have Vagrant up and running type ``` vagrant ssh ``` to log into your VM.  
* change to the /vagrant directory by typing ``` cd /vagrant ```. This will take you to the shared folder between your virtual machine and host machine.

* Type ``` ls ``` to ensure that you are inside the directory that contains project.py, database_setup.py, and two directories named 'templates' and 'static'

* Now type ``` python database_setup.py ``` to initialize the database.

* Type ``` python lotsofmenus.py ``` to populate the database with a sample of restaurants and menu items. (Optional)

* Type ``` python project.py ``` to run the Flask web server. In your browser visit **http://localhost:5000/restaurants** to view the restaurant menu app.  You should be able to view, add, edit, and delete menu items and restaurants.

### JSON Endpoints
The following JSON endpoints have been implemented to allow easier communication from the restaurant database

**http://localhost:5000/restaurants/JSON**  
This endpoint will list all the restaurants that are inthe database

**http://localhost:5000/restaurants/<int:restaurant_id>/menu/JSON/**  
This endpoint will list the menu items for a particular restaurant based on the restaurant_id

**http://localhost:5000/restaurants/<int:restaurant_id>/menu/<int:menu_id>/JSON/**  
This endpoint will list the the description of a particular menu item for a particular menu based on the restaurant_id and menu_id


## Authors

* **Christine Pinnkathok** 

## Acknowledgments

* I would like to thank the Udacity Course, Fullstack Web Development, as well as all the students who contributed to the knowledge center and slack channels. Tim Nelson is an amazing mentor and helped me work through a lot of difficult problems.  
*Installation and Running sections of ReadMe file taken from OAuth2.0 Read me file from Udacity Course, Fullstack Web Development
