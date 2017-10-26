Today
# Document a LAMP setup and Save the Image
---

## Part I: Setup Local and Remote Repository
---

### Create a Local Git Repo
- Create a new repo on your laptop called docs
	1. Create a folder called docs
	2. cd to docs
	3. run the command: git init to initialize the repository
	4. copy this document into the repo
	5. run: git status
	6. run: git add *
	7. run: git commit -m "First commit with lab guide"
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
		- run: git clone <http://my.repo.address>
		- This copies your remote repository to a local repo
		- Your local cloned repository will be set to use Github as a remote
	- Create an empty repository with a readme
		 - Copy the url of the new GitHub repo
			1. Click the link of your repository at the top of the page
			2. Click the Clone or Download green button
			3. Copy the URL of the repository  
			4. git remote add origin http://pathto/lamp_server_setup.git
			5. git push -u origin master
	- Note: git can be setup to automatically login with a push
	- With https it can be set to only ask for a password ever 8 hours, for example
		- git config --global remote.origin.url https://{username}@github.com/{username}/{repo_name}
	    - git config --global credential.helper 'cache --timeout=28000'
---

### Check Your GitHub Repo
- create a new directory elsewhere called test_lamp
- git clone [path_to_your_repo]
- does your new directory contain the same files as your local repo?
- if so then you have successfully completed part I
---

### Demo to Instructor that Part I is complete

## Part II Setup an Image in the Cloud (Digital Ocean Droplet)
- Guide: https://www.digitalocean.com/community/tutorials/initial-server-setup-with-centos-7
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
	- ssh root@[your_server_ip_address]
- Change the root password
	- enter the old password
	- enter the new password
	- enter the new password a second time to confirm
- Write down the root password
- Confirm to instructor you can log in and out with the root password
---

### Add a Second User
- Add a second user and set the password 
- adduser [username] 
- passwd [username]
- write down the username and password
- logout and back in to test the username
---

### Add second user to sudoers (ability to run admin functions)
- from second user account:
- su root
- enter the root password
- run: gpasswd -a [second_user_name] wheel
- run: exit
- run: sudo vi file.txt
- if vi runs, your second user has sudo access
- exit vi without saving

### Create SSH Keys Locally and Install Public Key on the server
- Create SSH keys locally (if you already have keys created skip this step)
    - on your local computer from the terminal run
    - ssh-keygen
- Copy the public key to your new server
	- either automatically with 
		- ssh-copy-id [username]@[server_ip_address] (you will be prompted for the password)
	- or manually by using the terminal of the local machine and
		- run: cat ~/.ssh/id_rsa.pub
		- copy the text (this is your public key) to your clipboard
		- login to the remote server as root
		- su to the second user (su - jimbo) for example if jimbo is the user you created 
		- run the following commands
			    mkdir .ssh
				chmod 700 .ssh
				vi .ssh/authorized_keys
				Enter :x then ENTER to save and exit the file.
				chmod 600 .ssh/authorized_keys
				exit
- Ensure you can login to your new user with the SSH key
	From the local machine ssh [seconduser]@[your_server_ip]
- Disable the root login
	- vi /etc/ssh/sshd_config
	- find this line
	- #PermitRootLogin yes
	- remove the # which makes this line active
	- change yes to no
	- Enter :x then ENTER to save and exit the file
	- reload ssh with this command:
	- systemctl reload sshd
---

## Part III Install Apache, MySQL, and Php
- Guide: https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-centos-7
- Install Apache with yum
- Install MySQL with yum
- Secure your MySQL installation
- Install a base Php (you do not have to install the addtional modules)
- You should not have to configure the firewall
- Create Hello World php script (hw.php)
---

## Part IV Creating a Snapshot of your image
- Using the terminal shutdown your server
- Using the Digital Ocean Interface Create a Snapshot of Your Server
- Name it LAMPwithKeys
---

## Part V Install Wordpress
- Guide: https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-on-centos-7
- Do you need wget ? yum install wget
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





