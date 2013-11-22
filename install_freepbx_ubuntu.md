~~~ Work in Progress ~~~

*Pull Requests gladly accepted.*

How To Install FreePBX on Ubuntu 12.04
====

### Introduction

Since the introduction of the private branch exchange (PBX) in the 1970s, companies have become dependent on the many features that were introduced over time. Today, businesses have a variety of options when it comes to telephone systems. Many are choosing the cloud with a hosted PBX solution in order to reduce capital expenses, maintenance &amp; upgrade costs &ndash; while still reaping the benefits of the latest PBX features.

This article aims to provide a guide through the (initial) planning and deployment of a FreePBX &amp; Asterisk VoIP server and assumes you will be starting from a base install of `Ubuntu 12.04`

## What is a PBX?

A PBX is a system that connects internal telephone extensions to outside public & mobile telephone networks. An IP (Internet Protocol) PBX is a PBX that provides audio, video, and instant messaging communication through data networks connected with the Public Switched Telephone Network (PSTN) for worldwide telephony communication.

In addition to the traditional PSTN (i.e. landline), modern PBX systems also utilize the Voice over Internet Protocol (VoIP). VoIP gateways enable businesses to use their managed intranets to help reduce long-distance expenses; and enjoy the benefits of a single network for voice and data communication.

## Asterisk IP PBX

Asterisk is an open source software implementation of a telephone PBX. Like any PBX, it allows connected telephones to make calls to one another, and to connect to other telephone services, such as traditional landlines (i.e. PSTN) and VoIP networks.

The Asterisk software includes many features available in proprietary PBX systems:

* Voice mail
* Conference calling
* Interactive voice response (IVR or *phone menus*)
* Automatic call distribution (ACD)
* Fax-to-email
* SIP Trunks
* and much, much more

In addition, users can create new functionality by writing dial-plan scripts in several of Asterisk's own extensions languages or by adding custom, loadable modules.

## FreePBX

FreePBX is an open source graphical user interface (GUI) that controls and manages Asterisk. Without FreePBX, Asterisk's configuration files could only be modified via the command line. FreePBX can be installed manually or as part of a pre-configured [Distro](https://www.digitalocean.com/community/articles/how-to-install-freepbx-on-centos-6-4).

## Preparatory Steps

First, you must make a few structural decisions, before you deploy your cloud PBX.

### Location of VoIP Server

Voice quality on VoIP calls is affected by network latency, among other factors. Thus, it is advisable to select a datacenter that is closest to the general, geographic location of the majority of your anticipated calls.

### Accessing VoIP Server After Deployment

To access your IP PBX server after deployment, you will need to open a web browser and navigate to your cloud server's IP address or fully qualified domain name (FQDN). If you wish to assign a FQDN to your VoIP server, make sure that you assign a FQDN as your server's hostname &ndash; when you create your DigitalOcean cloud server in the next step &ndash; via the DigitalOcean Control Panel.

### Server Specifications

Deciding on the best size machine for your cloud IP-PBX server is not an exact science. Try to best anticipate your average call volume; and then follow the steps outlined in: [How To Create Your First DigitalOcean Droplet Virtual Server](https://www.digitalocean.com/community/articles/how-to-create-your-first-digitalocean-droplet-virtual-server) to deploy a `64-bit Ubuntu 12.04` virtual private server (VPS).

>#### SSH Keys
>
For increased security, it is advisable that you:
* create your droplet with pre-installed SSH keys. *See* [How To Use SSH Keys with DigitalOcean Droplets](https://www.digitalocean.com/community/articles/how-to-use-ssh-keys-with-digitalocean-droplets) (**Windows users:** Refer to the article cited, blow); and
* disable password logins. *See* [How To Create SSH Keys with PuTTY to Connect to a VPS](https://www.digitalocean.com/community/articles/how-to-create-ssh-keys-with-putty-to-connect-to-a-vps).

Generally speaking, a:

*  512 MB droplet can support approximately `5-15` concurrent calls
*  1 GB supports approx. `15-25` concurrent calls
*  2 GB supports approx. `25-50` concurrent calls
*  4 GB supports approx. `50-100` concurrent calls
*  8 GB supports approx. `100-175` concurrent calls
*  16 GB supports approx. `175+` concurrent calls

**NOTE:** It is safe to "guess low." If you underestimate your call volume, try adding swap space. *See* [How To Add Swap on Ubuntu 12.04 | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-add-swap-on-ubuntu-12-04). In addition, DigitalOcean makes it easy to resize your cloud server, later. *See* [How To Resize Droplets Using Snapshots | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-resize-droplets-using-snapshots).

### Set the Hostname and FQDN in `/etc/hosts`

Next, follow the steps outlined in [Setting the Hostname & Fully Qualified Domain Name (FQDN) on Ubuntu 12.04 or CentOS 6.4](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/blob/master/set_hostname_fqdn_on_ubuntu_centos.md). If entered properly at the time that your cloud server was created, the machine's `hostname` should already be formatted as a FQDN. If so, feel free to skip to the section titled [Setting the Fully Qualified Domain Name (FQDN)](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/blob/master/set_hostname_fqdn_on_ubuntu_centos.md#setting-the-fully-qualified-domain-name-fqdn).

### Setting the Timezone

You can set your server's timezone to whatever you want. It may be best to set it to the same timezone as most of your callers. If you are unsure which timezone would be best, consider using universal coordinated time (UTC) &ndash; also known as `Greenwich Mean Time`.

To view the current timezone, execute:

	date

To change your Ubuntu server's timezone, execute the following command:

	sudo dpkg-reconfigure tzdata

and follow the on-screen prompts.

### Update Current Software

Now you need to install the available software updates for your Ubuntu server. Doing so patches security holes in packages and helps protect your droplet against unauthorized access.

	sudo apt-get update && sudo apt-get -y upgrade && sudo apt-get -y dist-upgrade && sudo apt-get -y autoremove

## Installation

We'll commence by installing required dependencies:

	sudo apt-get -y install build-essential linux-headers-`uname -r` openssh-server apache2 mysql-server mysql-client bison flex php5 php5-curl php5-cli php5-mysql php-pear php-db php5-gd curl sox libncurses5-dev libssl-dev libmysqlclient15-dev mpg123 libxml2-dev libnewt-dev sqlite3 libsqlite3-dev pkg-config automake libtool autoconf git subversion uuid uuid-dev

### Install PearDB

	sudo pear install db

**Note:** Ignore the `WARNING: "pear/DB" is deprecated in favor of "pear/MDB2"`

### Reboot Server

	sudo reboot now

### Install Dependencies for Google Voice

	cd /usr/src
	sudo wget https://iksemel.googlecode.com/files/iksemel-1.4.tar.gz
	sudo tar xf iksemel-1.4.tar.gz
	cd iksemel-1.4
	sudo ./configure
	sudo make
	sudo make install

### Install &amp; Configure Asterisk

We'll start with the Asterisk source files:

	cd /usr/src
	sudo wget http://downloads.asterisk.org/pub/telephony/dahdi-linux-complete/dahdi-linux-complete-current.tar.gz
	sudo wget http://downloads.asterisk.org/pub/telephony/libpri/libpri-1.4-current.tar.gz
	sudo wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-11-current.tar.gz

#### Compile &amp; Install DAHDI

	cd /usr/src
	sudo tar xvfz dahdi-linux-complete-current.tar.gz
	cd dahdi-linux-complete-2.6.1+2.6.1
	sudo make all
	sudo make install
	sudo make config

#### Compile &amp; Install LIBPRI

	cd /usr/src
	sudo tar xvfz libpri-1.4-current.tar.gz
	cd libpri-1.4.14
	sudo make
	sudo make install

#### Compile &amp; Install Asterisk

	cd /usr/src
	sudo tar xvfz asterisk-11-current.tar.gz
	cd asterisk-*
	sudo ./configure
	sudo contrib/scripts/get_mp3_source.sh
	sudo make menuselect
	sudo make
	sudo make install
	sudo make config

#### Install Asterisk-Extra-Sounds

	cd /var/lib/asterisk/sounds
	sudo wget http://downloads.asterisk.org/pub/telephony/sounds/asterisk-extra-sounds-en-gsm-current.tar.gz
	sudo tar xfz asterisk-extra-sounds-en-gsm-current.tar.gz
	sudo rm asterisk-extra-sounds-en-gsm-current.tar.gz

### Install &amp; Configure FreePBX

Download and extract FreePBX:

	export VER_FREEPBX=2.11
	cd /usr/src
	sudo svn co http://www.freepbx.org/v2/svn/freepbx/branches/${VER_FREEPBX} freePBX
	cd freePBX

#### Create the Asterisk user &amp; set ownership permissions

	sudo adduser asterisk --disabled-password --no-create-home --gecos "Asterisk User"
	sudo chown asterisk. /var/run/asterisk
	sudo chown -R asterisk. /etc/asterisk
	sudo chown -R asterisk. /var/{lib,log,spool}/asterisk
	sudo chown -R asterisk. /usr/lib/asterisk
	sudo mkdir /var/www/html
	sudo chown -R asterisk. /var/www/

#### Edit Apache

	sudo sed -i 's/\(^upload_max_filesize = \).*/\120M/' /etc/php5/apache2/php.ini
	sudo cp /etc/apache2/apache2.conf /etc/apache2/apache2.conf_orig
	sudo sed -i 's/^\(User\|Group\).*/\1 asterisk/' /etc/apache2/apache2.conf
	sudo service apache2 restart

### Configure Asterisk Database in MySQL

	export ASTERISK_DB_PW=amp109 
	mysqladmin -u root create asterisk -p
	mysqladmin -u root create asteriskcdrdb -p
	mysql -u root asterisk -p < SQL/newinstall.sql 
	mysql -u root asteriskcdrdb -p < SQL/cdr_mysql_table.sql

### Set Permissions on MySQL Database

	mysql -u root -p -e "GRANT ALL PRIVILEGES ON asterisk.* TO asteriskuser@localhost IDENTIFIED BY '${ASTERISK_DB_PW}';"
	mysql -u root -p -e "GRANT ALL PRIVILEGES ON asteriskcdrdb.* TO asteriskuser@localhost IDENTIFIED BY '${ASTERISK_DB_PW}';"
	mysql -u root -p -e "flush privileges;"

#### Restart Asterisk &amp; Install FreePBX

	sudo ./start_asterisk start
	sudo ./install_amp --webroot /var/www/freepbx
	sudo amportal a ma installall
	sudo amportal a reload

Finally, execute:

	sudo ln -s /var/lib/asterisk/moh /var/lib/asterisk/mohmp3
	sudo amportal start

### Confirm Successful Installation

To confirm that FreePBX &amp; Asterisk were installed successfully, open a web browser and navigate to your cloud server's FQDN or IP address, and you should be greeted by the FreePBX administrator-account setup screen.

![FreePBX Account Setup](http://i.imgur.com/AQPW20I.png)

### Create a FreePBX Admin User & Password

That username and password will be used in the future to access the FreePBX configuration screen, via the web GUI.

**Note:** This password does **not** change the underlying operating system's root password, if any. It is *only* used for access to the FreePBX web interface.

>#### Potential Post-Install Error

>After you enable and update the modules in FreePBX You might see the following error: **Symlink from modules failed**.
 
>To correct this error,

>1. Delete the list of failed files, by executing:
>
		cd /etc/asterisk
		sudo rm ccss.conf confbridge.conf features.conf sip.conf iax.conf logger.conf extensions.conf sip_notify.conf

>2.	Then in the FreePBX webUI go to the <code>Module Admin</code> and uninstall and reinstall the <code>Camp On</code> module.

>This should resolve the Symlink issue.

## Configure Mail Server

To configure Postfix &amp; Cyrus SASL, begin with **Step Two** of this article: [How To Install and Setup Postfix on Ubuntu 12.04 | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-install-and-setup-postfix-on-ubuntu-12-04).

## Security

Any server accessible from the public Internet should be security hardened, and an Asterisk IP-PBX is no exception. Security best practices, however, are not within the scope of this article.

## Additional Configuration

To continue tailoring your Asterisk VoIP server to your specific environment, check out the FreePBX [First Steps After Installation](http://wiki.freepbx.org/display/FD/FreePBX+Distro+First+Steps+After+Installation) guide.

## Additional Resources

* [Asterisk &amp; FreePBX Deployment Questionnaire](http://wiki.freepbx.org/display/HTGS/Asterisk+and+FreePBX+Deployment+Questionnaire#AsteriskandFreePBXDeploymentQuestionnaire-SystemGreetings)
* [FreePBX Wiki](http://wiki.freepbx.org/)
* [FreePBX Forums](http://www.freepbx.org/forums)
* [New FreePBX Users | Guide to Diagnosing Problems](http://www.freepbx.org/support/documentation/howtos/howto-new-freepbx-users-guide-to-diagnosing-problems)

<p><div style="text-align: right; font-size:smaller;">Article submitted by: <a href="https://plus.google.com/107285164064863645881?rel=author" target="_blank">Pablo Carranza</a> &bull; DATE</div></p>

~~~ Work in Progress ~~~

*Pull Requests gladly accepted.*
