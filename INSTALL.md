#TL;DR for building your node server...

## Roll an Ubuntu 10.04 LTS Lucid (EBS Boot) on EC2 then run...

	sudo apt-get update
	sudo chown -R www-data:www-data /var/www
	sudo apt-get install build-essential libssl-dev
	cd
	mkdir src
	git clone https://github.com/joyent/node.git src/node
	cd src/node
	git checkout v0.4.10
	./configure
	JOBS=2 make
	sudo make install
	sudo true && curl http://npmjs.org/install.sh | sudo sh
	sudo mkdir /var/your_node_project
	sudo chown www-data:www-data /var/your_node_project
	sudo -Hu www-data git clone git@github.com:your_account_here/your_node_project_here.git /var/your_node_project_here
	sudo -Hu www-data ssh-keygen -t rsa  # chose "no passphrase"
	sudo cat /var/www/.ssh/id_rsa.pub
# Add the key as a "deploy key" at https://github.com/your_account_here/your_node_project_here/admin
	sudo -Hu www-data git clone git@github.com:your_account_here/your_node_project_here.git /var/your_node_project_here
	sudo chmod ugo+x /var/your_node_project_here/update
	sudo /var/your_node_project_here/update
	sudo ln -s /lib/init/upstart-job /etc/init.d/your_node_project_here
	sudo apt-get install monit
	sudo nano /etc/monit/monitrc
# Config the file to your taste including monitoring a pid file in /var/your_node_project/run/your_node_project.pid
	sudo monit -t
# If all checks out...
	sudo reboot

#Detailed instructions

# Setup and installation

First of all, make a clone or [fork of this repository](http://help.github.com/fork-a-repo/) and replace all occurrences of `your_node_project` with a name you want to refer to your project with throughout the server.  Also replace `your_account_here` with your account name from github.

## Launch an EC2 instance

[Start a "micro" Amazon EC2 instance](https://console.aws.amazon.com/ec2/home) and use one of the following AMIs, depending on where you chose to launch it:

We use the awesome Ubuntu 10.04 LTS Lucid (EBS Boot) from alestic (http://alestic.com/) in 64 bit flavour

US East 1: `ami-63be790a`
US West 1: `ami-97c694d2`
EU West 1: `ami-5c417128`
AP SouthEast 1: `ami-4af18918`
AP NorthEast 1: 'ami-34d36635'

Go with the defaults in the "wizard" presented. Chose to create a new key pair when asked and **be sure to make a secure backup of the private key** that you will download. A good place to put your private key is in `~/.ssh/myapp.pem` and then `chmod 0600 ~/.ssh/myapp.pem` so no one else can read it but you.

When the instance is green and "started", log in to the machine:

    ssh -i ~/.ssh/myapp.pem ubuntu@XXX.amazonaws.com

*Note: Replace `XXX.amazonaws.com` with the hostname or address of your instance.*

*Note: If you are running Microsoft Windows, which lacks an SSH client, see [WINDOWS-SSH.md](WINDOWS-SSH.md#readme)*.

## Update server

	sudo apt-get update
	
## Create our directory
	sudo chown -R www-data:www-data /var/www

## Install stable Node.js:

	sudo apt-get install build-essential libssl-dev
	cd
	mkdir src
	git clone https://github.com/joyent/node.git src/node
	cd src/node
	git checkout v0.4.10
	./configure
	JOBS=2 make
	sudo make install

NPM:

    sudo true && curl http://npmjs.org/install.sh | sudo sh

## Checkout your source

    sudo mkdir /var/myapp
    sudo chown www-data:www-data /var/myapp

If your git repository is public (i.e. viewable by anyone):

    sudo -Hu www-data git clone https://github.com/your_account_here/your_node_project_here.git /var/your_node_project

If your git repository is private:

    sudo -Hu www-data ssh-keygen -t rsa  # chose "no passphrase"
    sudo cat /var/www/.ssh/id_rsa.pub
    # Add the key as a "deploy key" at https://github.com/your_account_here/your_node_project/admin
    sudo -Hu www-data git clone git@github.com:your_account_here/your_node_project_here.git /var/your_node_project

## Configure upstart

TODO: add the stuff here

## Install monit Monit
sudo apt-get install monit
sudo nano /etc/monit/monitrc

## Configure monit
#config the file to your taste including monitoring a pid file in /var/your_node_project/run/your_node_project.pid and your log in  
#/var/log/your_node_project.log

check process node with pidfile /var/your_node_project/run/your_node_project.pid
        start = "/sbin/start your_node_project"
        stop = "/sbin/start stop your_node_project"

    if changed pid then alert
    if 5 restarts within 5 cycles then timeout

check file log with path /var/log/your_node_project.log
	if timestamp > 60 seconds then alert

#confirm your config is good

sudo monit -t

## Done

Your web app should now be operational.

If everything works, **continue by reading [WORKFLOW.md](https://github.com/ctmaclean/ec2-webapp/blob/master/WORKFLOW.md#readme)**
