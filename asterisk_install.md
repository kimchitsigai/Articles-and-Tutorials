#### ~~~ WIP ~ Draft ~ WIP ~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls)* gladly accepted
How to Install Asterisk 11 on Ubuntu 12.04
=

### Introduction



## Pre-deployment Planning

First, you must make a few structural decisions, before you deploy your cloud PBX.

#### Location of VoIP Server

Voice quality on VoIP calls is affected by network latency, among other factors. Thus, it is advisable to select a datacenter that is closest to the general, geographic location of the majority of your anticipated calls.

#### Accessing VoIP Server After Deployment

To access your IP PBX server after deployment, you will need to open a web browser and navigate to your cloud server's IP address or fully qualified domain name (FQDN). If you wish to assign a FQDN to your VoIP server, make sure that you assign a FQDN as your server's hostname &ndash; when you create your DigitalOcean cloud server in the next step &ndash; via the DigitalOcean Control Panel.

## Server Setup

1. Follow the steps outlined in [How To Create Your First DigitalOcean Droplet Virtual Server](https://www.digitalocean.com/community/articles/how-to-create-your-first-digitalocean-droplet-virtual-server) to deploy the latest release of an `Ubuntu 12.04 server`.

	>#### SSH Keys
	>
	>For increased security, it is advisable that you:
	>
	>* Create your droplet with pre-installed SSH keys. *See* [How To Use SSH Keys with DigitalOcean Droplets](https://www.digitalocean.com/community/articles/how-to-use-ssh-keys-with-digitalocean-droplets) (**Windows users:** Refer to the article cited, next); **and**
	>* Disable password logins. *See* [How To Create SSH Keys with PuTTY to Connect to a VPS](https://www.digitalocean.com/community/articles/how-to-create-ssh-keys-with-putty-to-connect-to-a-vps).

2. Follow the steps in [Initial Server Setup with Ubuntu 12.04 | DigitalOcean](https://www.digitalocean.com/community/articles/initial-server-setup-with-ubuntu-12-04).

#### Hostname & FQDN

Set your server's hostname and Fully Qualified Domain Name by implementing the steps in [Setting the Hostname & Fully Qualified Domain Name (FQDN) on Ubuntu 12.04 or CentOS 6.4](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/blob/master/set_hostname_fqdn_on_ubuntu_centos.md).

#### Timezone

You can change your server's timezone to whatever you want; altough it may be best to set it to the same timezone of most of your users. To do so, simply execute, in a terminal:

    sudo dpkg-reconfigure tzdata

and follow the instructions in the ensuing, on-screen prompts.

## Update Your System

To make sure that your server operating system (OS) is up to date, execute:

	sudo apt-get update && sudo apt-get -y upgrade && sudo apt-get -y dist-upgrade && sudo apt-get -y autoremove && sudo reboot now

When the update completes, the server will reboot.

## Install Asterisk Dependencies

Next, execute:

	sudo apt-get -y install build-essential wget libssl-dev libncurses5-dev libnewt-dev  libxml2-dev linux-headers-$(uname -r) libsqlite3-dev uuid-dev

## Download Source Files

Even though Asterisk is packaged in Ubuntu's software repositories, the Asterisk release housed in the repos is out-dated. To download &amp; extract the most-recent versions of DAHDI, libpri &amp; Asterisk, execute:

	cd /usr/src/ && sudo wget -O - "http://downloads.asterisk.org/pub/telephony/dahdi-linux-complete/dahdi-linux-complete-current.tar.gz" | sudo tar zxvf - && sudo wget -O - "http://downloads.asterisk.org/pub/telephony/libpri/libpri-1.4-current.tar.gz" | sudo tar zxvf - && sudo wget -O - "http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-11-current.tar.gz" | sudo tar zxvf -

## Install DAHDI

Now, execute:

	cd /usr/src/dahdi-linux-complete* && sudo make && sudo make install && sudo make config

## Install libpri

Next, execute:

	cd /usr/src/libpri* sudo make && sudo make install

## Install Asterisk

Finally, execute:

>During the installation, the system will present an <code>Asterisk Module and Build Option Selection</code> menu. For a default install, tap on the <code>Tab</code> to highlight <code>Save & Exit</code>, and press <code>Enter</code> to allow the install to continue.

	cd /usr/src/asterisk* && sudo ./configure && sudo make menuselect && sudo make && sudo make install && sudo make config && sudo make samples

## Start PBX Services

To start DAHDI, execute:

	sudo service dahdi start

To start Asterisk:

	sudo service asterisk start

## Verify Successful Install

To connect to the Asterisk CLI, execute:

	sudo asterisk -rvvv

## Configure Outgoing-Mail Server

To receive email notifications from your Asterisk server, follow the steps outlined in [How To Install a Send-Only Mail Server with Exim on Ubuntu 12.04 | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-install-the-send-only-mail-server-exim-on-ubuntu-12-04).

## Security

Any server accessible from the public Internet should be security hardened, and an Asterisk IP-PBX is no exception. Security best practices, however, are not within the scope of this article.

## Additional Resources

As always, if you need help with the steps outlined in this How-To, look to the DigitalOcean Community for assistance by posing your question(s), below.

<p><div style="text-align: right; font-size:smaller;">Article submitted by: <a href="https://plus.google.com/107285164064863645881?rel=author" target="_blank">Pablo Carranza</a> &bull; DATE</div></p>

# ~~~ WIP ~ DRAFT ~ WIP ~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls)* gladly accepted