# 1. Install CentOS 7 on Virtualbox

## How to install the CentOS on Virtualbox:

####  The things that you will need.

- [Download the DVD ISO Image](http://isoredirect.centos.org/centos/8/isos/x86_64/CentOS-8-x86_64-1905-dvd1.iso)
- [Virtual Box](https://www.virtualbox.org/wiki/Downloads)

---

### -  Step 1

Install the virtual box and open it. Click on the new to setup the new VM, select the Linux and then Red Hat, since CentOS is the clone of Red Hat and it uses the similar architecture.

In the next window, allocate the memory size. Allocate at least 1024MB; you can allocate more if you have enough memory. Create the virtual disk (VDI) and move to the next screen. Fixed size of the disk is not recommended in any scenario because you will be downloading many packages to run various applications. Select the dynamically allocated, make sure that your hard drive has enough free space. The 15GB space is sufficient enough to start with, select and then finish the setup.

### -  Step 2

The ISO image of the downloaded CentOS has to be linked to the newly created virtual machine, click on the setting storage empty browse the ISO image

Enable the network adapter so that it can use the internet to download the required packages.

Start the virtual machine.

### -  Step 3

Select the option `Install CentOS Linux 7` and hit enter. It will take time to load the packages, and you will see the opening screen of the server. It requires the basic information to set up the server, for example:

- Time zone
- Keyboard layout
- Language support 
- Installation source
- Software selection (**important**)
- Installation destination

Once you have finished providing the basic information (Language, keyboard, date & time), click on the installation source to confirm the ISO image that we have linked in step 2. Verify the installation source, go back and click on the **software selection** option.

Select the desired option; for example, if you want to create an infrastructure server to run the complete network operation, select this option to get all the required packages installed. Similarly, it goes to the other option as well. Select the **server with GUI** to get the server installed with the graphical interface option, you can select the add-ons at this moment, or you will get it whenever you need them. Click done, it will check the software dependencies, and you will see the confirmation in a couple of seconds.

Click on the installation destination, and select the newly created disk space. In the network and hostname setting, enable the network. You are done!

Begin the installation.

In the next screen, you will be asked to set up the root and username details. Select the strong root password and create your user.

It will take time, sit back and see the installing operation.


### -  Step 4

After finishing the setup, you will see the login screen

Click on not listed and then login using the root credentials, root access is required because we will be installing the repository. Click on the Application  Run terminal

On your terminal hit the following command to get the entire repository installed:

	yum install epel release 

EPEL (Extra package for Enterprise Linux) is an open source and free repository; it provides 100% high-quality add-on software packages for Linux distributions including Red Hat, CentOS, and Fedora.

The CentOS 7 server is ready with the most important repository. In this article we have discussed the process to get the CentOS installed; in the later article of this series, we will be discussing the configuration of other kinds of server-side software.


---

# 2. Update CentOS to Latest Version


### -  Step 1

Check current CentOS version: 

	cat /etc/redhat-release

### -  Step 2

Verify data and backups


### -  Step 3

Check for available updates:

	sudo yum check-update

### -  Step 4

Package manager cleanup:	

	sudo yum clean all

### -  Step 5

Update CentOS: 
	
	sudo yum update

### -  Step 6

Verify current CentOS version:

	cat /etc/redhat-release

---

# 3. Installing Terraform on CentOS 7


> Terraform is a tool for building, changing, and versioning infrastructure safely and efficiently. Terraform can manage existing and popular service providers as well as custom in-house solutions.

### - Step 1

First up, you will need to upgrade your system and packages to current version:

	sudo yum update

### - Step 2

Next, we will install wget and unzip packages if they're not already installed:

	sudo yum install wget unzip

### - Step 3

Download Terraform

For current version check [this](https://www.terraform.io/downloads.html) page.

	wget https://releases.hashicorp.com/terraform/0.11.13/terraform_0.11.13_linux_amd64.zip

### - Step 4

Next, we will unpack the archive to /usr/local/bin/

	sudo unzip ./terraform_0.11.13_linux_amd64.zip -d /usr/local/bin/

### - Step 5

All done. The only thing that is left now, is to check if terraform is successfully installed, with the following command:

	terraform -v

---

# 4. How To Create a Sudo User

#### Introduction

> The **sudo** command provides a mechanism for granting administrator privileges, ordinarily only available to the root user, to normal users. This guide will show you the easiest way to create a new user with sudo access on CentOS, without having to modify your server's **sudoers** file.

### - Step 1

Log in to your server as the root user.

	ssh root@server_ip_address

### - Step 2

Use the `adduser` command to add a new user to your system.

	adduser username

Use the passwd command to update the new user's password.

	passwd username

### - Step 3

Use the usermod command to add the user to the wheel group.

	usermod -aG wheel username

> By default, on CentOS, members of the `wheel` group have sudo privileges.

---

# 5. How to run sudo command without a password

### - Step 1

Type the following command as root user:

	visudo


### - Step 2

Append the following entry to run ALL command without a password for a user named tom:

	tom ALL=(ALL) NOPASSWD:ALL

Here is my sample config file:

![image](https://www.cyberciti.biz/media/new/faq/2015/05/execute-sudo-without-Password.jpg)

---

# 6. How to Setup ZSH and use it on a new user

### - Step 1

To install zsh from the repository, use the following commands:

	yum install zsh

### - Step 2

Create a new user:
	
	useradd -m another

### - Step 3

After the installation is complete, change the default shell of the **another** user to zsh with the chsh command below.

	chsh -s /bin/zsh another

### - Step 4

Check the current shell used with the command below:

	echo $SHELL

---

# 7. How to to run sudo with only `cat`

### - Step 1

Edit the /etc/sudoers file by typing the visudo command:

	sudo visudo

### - Step 2

Append/edit the line as follows in the /etc/sudoers file for user named 'another' to run '/bin/cat' :

	another	ALL=/usr/bin/cat

---

# 8. Create a Custom systemd Service

### - Step 1

Create a script or executable that the service will manage. This guide uses a simple Bash script as an example:

	DATE=`date '+%Y-%m-%d %H:%M:%S'`
	echo "Example service started at ${DATE}" | systemd-cat -p info

	while :
	do
	echo "Looping...";
	sleep 30;
	done

> This script will log the time at which it is initialized, then loop infinitely to keep the service running.

### - Step 2

Copy the script to **/usr/bin** and make it executable:

	sudo cp test_service.sh /usr/bin/test_service.sh
	sudo chmod +x /usr/bin/test_service.sh

### - Step 3

Create a Unit file to define a systemd service:

	[Unit]
	Description=Example systemd service.

	[Service]
	Type=simple
	ExecStart=/bin/bash /usr/bin/test_service.sh

	[Install]
	WantedBy=multi-user.target

> This defines a simple service. The critical part is the ExecStart directive, which specifies the command that will be run to start the service.

### - Step 4

Copy the unit file to /etc/systemd/system and give it permissions:

	sudo cp myservice.service /etc/systemd/system/myservice.service
	sudo chmod 644 /etc/systemd/system/myservice.service

### - Step 5

Start and Enable the Service:

	sudo systemctl start myservice
	sudo systemctl enable myservice

or:
	
	sudo systemctl enable --now myservice

---


# 9. How to install LAMP on CentOS 7

### - Step 1

**Install Apache**

> The Apache web server is currently the most popular web server in the world, which makes it a great default choice for hosting a website.

	sudo yum install httpd

Once it installs, you can start Apache on your VPS:

	sudo systemctl start httpd.service


### - Step 2

**Install MySQL (MariaDB)**

> Now that we have our web server up and running, it is time to install MariaDB, a MySQL drop-in replacement. MariaDB is a community-developed fork of the MySQL relational database management system.

	sudo yum install mariadb-server mariadb

When the installation is complete, we need to start MariaDB with the following command:

	sudo systemctl start mariadb

Now that our MySQL database is running, we want to run a simple security script that will remove some dangerous defaults and lock down access to our database system a little bit. Start the interactive script by running:

	sudo mysql_secure_installation

### - Step 3

**Install PHP**

> PHP is the component of our setup that will process code to display dynamic content. It can run scripts, connect to our MySQL databases to get information, and hand the processed content over to our web server to display.



We can once again leverage the `yum` system to install our components. We're going to include the php-mysql package as well:

	sudo yum install php php-mysql

This should install PHP without any problems. We need to restart the Apache web server in order for it to work with PHP. You can do this by typing this:

	sudo systemctl restart httpd.service

---

# 10. How to setup a LAMP development environment in Vagrant
    
### - Step 1

Create a directory where we would be creating the instance:

	mkdir -p ~/Vagrant/lamp
	cd  ~/Vagrant/lamp

Now we are required to initialize the **Vagrant** box.

### - Step 2

Can re-use Ubuntu 16.04 LTS (Xenial Xerus):

	vagrant init ubuntu/xenial64

Now do:

	vagrant up

> This will create a VagrantFile


### - Step 3

Edit the VagrantFile as below:

	et ft=ruby :

	VAGRANTFILE_API_VERSION = "2"

	Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

	  config.vm.box = "ubuntu/trusty64"

	  config.vm.provider "virtualbox" do |v|
	  	v.name = "Box Name"
	  end

	  config.vm.network "private_network", ip: "192.168.0.2"

	  config.vm.synced_folder "./", "/var/www/html"

	  config.vm.provision :shell, path: "config.sh"

	end


### - Step 4

Let us now start provisioning the LAMP installation. For that, create a simple shell script named script.sh using your favorite text editor as:

	#!/bin/bash
	sudo apt-get -y update
	sudo apt-get -y install apache2
	sudo debconf-set-selections <<< 'mysql-server-5.5 mysql-server/root_password password rootpass'
	sudo debconf-set-selections <<< 'mysql-server-5.5 mysql-server/root_password_again password rootpass'
	sudo apt-get -y install mysql-server libapache2-mod-auth-mysql php5-mysql


### - Step 5

After saving the script.sh, run:

	vagrant up

### - Step 6

Now after the vagrant is done with installation, you are ready to go. SSH into the vagrant box as:

	vagrant ssh

---
