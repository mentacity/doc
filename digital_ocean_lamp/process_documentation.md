Today
# Document a LAMP setup and Save the Image
---

## Part I: Setup Local and Remote Repository
---

### Create a Local Git Repo
- Create a new repo on your laptop called docs
	- Create a folder called docs
	```shell
	  cd docs
    git init to initialize the repository
    copy this document into the repo
    git status
    git add *
    git commit -m "First commit with lab guide"
    ```
---

### Create a New GitHub Repository Called lamp_server_setup
- Under repositorys click the New button which is green
- Give the repository a name
- Select Public or Private (public by default, private requires subscription)
- Either
	- Create with a readme and clone the repository
		- Create with readme
		- Copy the url of the new GitHub repo by clicking the Clone or Download green button
		- On your local computer change directories to the folder where you want to store your repo
```
git clone [http://my.repo.address>]
```
		- This copies your remote repository to a local repo
		- Your local cloned repository will be set to use Github as a remote
	- Create an empty repository with a readme
		 - Copy the url of the new GitHub repo
			1. Click the link of your repository at the top of the page
			2. Click the Clone or Download green button
			3. Copy the URL of the repository  
			````
			git remote add origin http://pathto/lamp_server_setup.git
			git push -u origin master
			```
	- Note: git can be setup to automatically login with a push
	- With https it can be set to only ask for a password every 8 hours, for example
	```
		git config --global remote.origin.url https://{username}@github.com/{username}/{repo_name}
	    git config --global credential.helper 'cache --timeout=28000'
	```
---

### Check Your GitHub Repo
- create a new directory elsewhere called test_lamp
```
git clone [path_to_your_repo]
```
- does your new directory contain the same files as your local repo?
- if so then you have successfully completed part I
---

### Demo to Instructor that Part I is complete

## Part II Setup an Image in the Cloud (Digital Ocean Droplet)
- Guide:
```
https://www.digitalocean.com/community/tutorials/initial-server-setup-with-centos-7
```
---

### Create a Droplet and Reset the Root Password
- Create a new droplet
	- Sign in to Digital Ocean
	- Click Droplets in the menu at the top left
	- Click the green Create button in the top right
	- Select Droplet
	- From the create droplet screen choose your OS -- we are using CentOS 7
	- Choose a size: you can use the lowest resource droplet @ 5/month
	- Skip the rest of the options except for hostname
	- I used cent512-base
	- Check email for message from Digital Ocean containing root password for new server
	- Copy the root password and IP address into a text file


- Use SSH to login as root (use the emailed root password)
```
	ssh root@[your_server_ip_address]
```
- Change the root password
	- enter the old password
	- enter the new password
	- enter the new password a second time to confirm
- Write down the root password
- Confirm to instructor you can log in and out with the root password
---

### Add a Second User
- Add a second user and set the password
```
	adduser [username]
	passwd [username]
```

- write down the username and password
- logout and back in to test the username
---

### Add second user to sudoers (ability to run admin functions)
- from second user account:
```
	su root
```
- enter the root password
	```
	gpasswd -a [second_user_name] wheel
	exit
	sudo vi file.txt
	```
- if vi runs, your second user has sudo access
- exit vi without saving

### Create SSH Keys Locally and Install Public Key on the server
- Create SSH keys locally (if you already have keys created skip this step)
    - on your local computer from the terminal run
```
		ssh-keygen
```
    ssh-keygen should set folder permissions for ~/.ssh correctly but for reference:
	- Local .ssh folder permissions should be
		```
		chmod 700 .ssh
		chmod 644 id_rsa.pub
		```
		Your private key (id_rsa) should be 600 (-rw-------)
---

- Copy the public key to your new server
	- either automatically with
    ```
		ssh-copy-id [username]@[server_ip_address]
		```
		- (you will be prompted for the password)
	- or manually by using the terminal of the local machine and
```
		cat ~/.ssh/id_rsa.pub
```		
		- copy the text (this is your public key) to your clipboard
		- login to the remote server as root
		- su to the second user (su - jimbo) for example if jimbo is the user you created
		- run the following commands
    ```
		mkdir .ssh
		chmod 700 .ssh
		vi .ssh/authorized_keys
		```
		- Enter :x then ENTER to save and exit the file.
		```
		chmod 600 .ssh/authorized_keys
		exit
		```
- Ensure you can login to your new user with the SSH key
	From the local machine ssh
	```
	[seconduser]@[your_server_ip]
	```
- Disable the root login
	```
		vi /etc/ssh/sshd_config
	```
	-find this line
	```
	#PermitRootLogin yes
	```

	- remove the # which makes this line active
	- change yes to no
	- Enter :x then ENTER to save and exit the file
	- reload ssh with this command:
	```
		systemctl reload sshd
	```
---

## Part III Install Apache, MySQL (MariaDB), and Php
- Guide: https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-centos-7
- Install Apache with yum
	```
	sudo yum update
	sudo yum install httpd
	sudo systemctl start httpd.service
	```
- Test apache by opening your browser and navigating to:
	```
		http://your_server_IP_address/
	```
- Enable Apache to start automatically at boot
	```
		sudo systemctl enable httpd.service
	```
- Install MariaDB with yum
	```
	sudo yum update
  sudo yum install mariadb-server mariadb
  sudo systemctl start mariadb
  ```
- Secure your MySQL installation
	```
	sudo mysql_secure_installation
	```
	- you will be prompted for the default password: it should be blank
	- press enter
	- set a new password for the MariaDB root user (this is the db root user, not the machine root user)
	- press enter to answer Y to all the followup questions (answering yes to these questions increases security of the DB)
	- add automatic startup of MariaDB at boot by typing:
	```
		sudo systemctl enable mariadb.service
	```

- Install a base Php (you do not have to install the addtional modules)
```
	sudo yum install php php-mysql
```
	- restart Apache by typing:
```
	sudo systemctl restart httpd.service
```

- You should not have to configure the firewall
- Create Hello World php script (hw.php)
	```
		sudo vi /var/www/html/info.php
	```
	press i to insert into the file
	add
	```
		<?php phpinfo(); ?>
	```
	into the file
	press :x to save
---

## Part IV Creating a Snapshot of your image
- Using the terminal shutdown your server
- Using the Digital Ocean Interface Create a Snapshot of Your Server
- Name it LAMPwithKeys
- Create a new droplet using the existing snapshot
- Your ssh key and second user will work but you will still have to use the
  password sent in the email for root and set a new password on first root login
---

## Part V Install Wordpress
- Guide: https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-on-centos-7
	- Open a MySQL command prompt
```
	mysql -u root -p
```
	- Use the following commands with your options, for example whatever database, user, password you choose
	```
	CREATE DATABASE wordpress;
	CREATE USER wordpressuser@localhost IDENTIFIED BY 'password';
	GRANT ALL PRIVILEGES ON wordpress.* TO wordpressuser@localhost IDENTIFIED BY 'password';
	FLUSH PRIVILEGES
	exit
	```
- install a php module to allow WP to resize images
	```
	sudo yum update
	sudo yum install php-gd
	sudo service httpd restart
	```
- Do you need wget ? yum install wget
	```
	sudo yum wget
	cd ~
	wget http://wordpress.org/latest.tar.gz
	tar xzvf latest.tar.gz
	sudo rsync -avP ~/wordpress/ /var/www/html/
	mkdir /var/www/html/wp-content/uploads
	sudo chown -R apache:apache /var/www/html/*
	cd /var/www/html
	cp wp-config-sample.php wp-config.php
```
	  - I think the nano editor is easier than vi. Install it?
    ```
    sudo yum update
    sudo install nano
	```
	- Now you can use nano to edit wp-config
	`nano wp-config.php`

- Fill in the values of these parameters with the information for the database that you created.
- The following goes inside your wp-config.
```
	// ** MySQL settings - You can get this info from your web host ** //
	/** The name of the database for WordPress */
	define('DB_NAME', 'wordpress');

	/** MySQL database username */
	define('DB_USER', 'wordpressuser');

	/** MySQL database password */
	define('DB_PASSWORD', 'password');
```
- navigate to the wordpress setup wizard
	```
		http://server_domain_name_or_IP
	```
- finish setup
- login as admin
	`http://server_domain_name_or_IP/wp-admin`
- Test Wordpress
- Create Another Snapshot call it WordPressImage
---

## Part VI Add Encryption with OpenSSL
- Guide: https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-apache-in-ubuntu-16-04
- The guide is for Ubuntu, but should work for CentOS
- Create a final snapshot called WPwithEncryption
---

## Part VII Initialize the Web Directory as a Git Repo
- Make sure git is installed by typing the git command (you will get options if installed)
- If git is not installed, install it
-

## Part VIII
- Create a new droplet based on your final snapshot
- Test your WordPress and Encryption by browsing to https://your_ip_address
---
