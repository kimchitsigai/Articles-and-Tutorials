# ~~~~~~~~~~ DRAFT ~~~~~~~~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls) gladly accepted*

Deploy a VoIP Private Branch Exchange (PBX) Server on CentOS 6.4
====

### Introduction

Since the introduction of the private branch exchange (PBX) in the 1970s, companies have become dependent on the many features that were introduced over time. Today, businesses have a variety of options when it comes to telephone systems. Many are choosing the cloud with a hosted PBX solution in order to reduce capital expenses, maintenance & upgrade costs &ndash; while still reaping the benefits of the latest PBX features.

This article aims to provide a guide through the planning and deployment of a [FreePBX](http://www.freepbx.org/) and [Asterisk](http://www.asterisk.org/) VoIP server and assumes you will be starting from a base install of `CentOS 6.4`.

## What is a PBX?

A PBX is a system that connects internal telephone extensions to outside public & mobile telephone networks. An IP (Internet Protocol) PBX is a PBX that provides audio, video, and instant messaging communication through data networks connected with the Public Switched Telephone Network (PSTN) for worldwide telephony communication.

In addition to the traditional PSTN (i.e. landline), modern PBX systems also utilize the Voice over Internet Protocol (VoIP). VoIP gateways enable businesses to use their managed intranets to help reduce long-distance expenses; and enjoy the benefits of a single network for voice and data communication.

## Asterisk IP PBX

Asterisk is an open source software implementation of a telephone PBX. Like any PBX, it allows connected telephones to make calls to one another, and to connect to other telephone services, such as traditional landlines (i.e. PSTN) and VoIP networks.

The Asterisk software includes many features available in proprietary PBX systems:

* voice mail
* conference calling
* interactive voice response (phone menus)
* automatic call distribution
* and much, much more

In addition, users can create new functionality by writing dial-plan scripts in several of Asterisk's own extensions languages or by adding custom, loadable modules.

## Preparatory Steps

First, you must make a few structural decisions, before you deploy your cloud PBX.

### Location of VoIP Server

Voice quality on VoIP calls is affected by network latency, among other factors. The standard for traditional landlines is a latency of 45 milliseconds. With VoIP, good call quality can be achieved with a latency of a 75 ms to 100 ms delay. Thus, it is advisable to select a datacenter that is closest to the general, geographic location of the majority of your anticipated calls.

### Accessing VoIP Server After Deployment

To access your IP PBX server after deployment, you will need to open a web browser and navigate to your cloud server's IP address or fully qualified domain name (FQDN). If you wish to assign a FQDN to your VoIP server, make sure that you use a FQDN as your server's Hostname, when you create it (in the next step) via the [DigitalOcean Control Panel](https://www.digitalocean.com/community/articles/the-digitalocean-control-panel). 

### Server Specifications

Deciding on the best size machine for your cloud IP-PBX server is not an exact science. Try to best anticipate your average call volume; and then follow the steps outlined in: [How To Create Your First DigitalOcean Droplet Virtual Server](https://www.digitalocean.com/community/articles/how-to-create-your-first-digitalocean-droplet-virtual-server) to deploy a `64-bit CentOS 6.4` virtual private server (VPS).

>#### SSH Keys
>
For increased security, it's advisable that you create your droplet with pre-installed SSH keys. *See* [How To Use SSH Keys with DigitalOcean Droplets](https://www.digitalocean.com/community/articles/how-to-use-ssh-keys-with-digitalocean-droplets) or (for Windows users) [How To Create SSH Keys with PuTTY to Connect to a VPS](https://www.digitalocean.com/community/articles/how-to-create-ssh-keys-with-putty-to-connect-to-a-vps).

Generally speaking, a:

*  512 MB droplet supports approximately `5-15` concurrent calls
*  1 GB supports approx. `15-25` concurrent calls
*  2 GB supports approx. `25-50` concurrent calls
*  4 GB supports approx. `50-100` concurrent calls
*  8 GB supports approx. `100-175` concurrent calls
*  16 GB supports approx. `175+` concurrent calls

**NOTE:** It is safe to "guess low." If you underestimate your call volume, try adding swap space. *See* [How To Add Swap on CentOS 6](https://www.digitalocean.com/community/articles/how-to-add-swap-on-centos-6). In addition, DigitalOcean makes it easy to resize your cloud server, later. *See* [How To Resize Droplets Using Snapshots](https://www.digitalocean.com/community/articles/how-to-resize-droplets-using-snapshots).

### Set the Hostname and FQDN in `/etc/hosts`

Next, follow the steps outlined in [Setting the Hostname & Fully Qualified Domain Name (FQDN) on Ubuntu 12.04 or CentOS 6.4](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/blob/master/set_hostname_fqdn_on_ubuntu_centos.md). If entered properly at the time that your cloud server was created, the machine's `hostname` should already be formatted as a FQDN. If so, feel free to skip to the section titled **Setting the Fully Qualified Domain Name (FQDN)**

### Setting the Timezone

You can set your server's timezone to whatever you want. It may be best to set it to the same timezone as most of your callers. If you are unsure which timezone would be best, consider using universal coordinated time (UTC) &ndash; also known as `Greenwich Mean Time`.

To view the current timezone, execute:

	date

To change your CentOS server's timezone, you need to create a symbolic link from a zone file in `/usr/share/zoneinfo/` to `/etc/localtime`. In doing so, you first need to find the zone file for your timezone (see the examples below for common possibilities):

#### Universal Coordinated Time:

	ln -sf /usr/share/zoneinfo/UTC /etc/localtime 

#### Eastern Standard Time:

	ln -sf /usr/share/zoneinfo/EST /etc/localtime

#### American Central:

	ln -sf /usr/share/zoneinfo/US/Central /etc/localtime 

#### American Eastern:

	ln -sf /usr/share/zoneinfo/US/Eastern /etc/localtime

### Update Current Software

Now you need to install the available software updates for your CentOS server. Doing so patches security holes in packages and helps protect your droplet against unauthorized access.

	yum update

## Installation

First, navigate to your server's `/tmp` directory by executing the following command:

	cd /tmp

Next, download the FreePBX Distro [installation script](http://wiki.freepbx.org/display/FD/Converting+Stock+Centos+to+a+FreePBX+Distro) by executing:

	wget http://upgrades.freepbxdistro.org/blank-centos-installer/4.211.64-track/4.211.64-1-Installer-Script.sh

>This script assumes you have a base install of CentOS 6.4. It will convert your system to a FreePBX Distro.

Then, make the script executable:

	chmod +x 4.211.64-1-Installer-Script.sh

Run the installer script, by executing:

	./4.211.64-1-Installer-Script.sh

Once completed you should have a full functioning  IP-PBX server, based on the [FreePBX Distro](http://wiki.freepbx.org/display/FD/FreePBX+Distro+Home).

## Update your FreePBX Distro

FreePBX.org publishes update scripts for new FreePBX Distro releases on its [website](http://wiki.freepbx.org/display/FD/FreePBX-Distro-4.211.64). It is advisable that you bookmark that page and refer to it on a regular basis to obtain any recent upgrade scripts that may have been made available.

To update your FreePBX Distro to the most recent release, you will need to execute a series of individual shell scripts AND do so in sequential order.

### Check Current FreePBX Distro Version

In a terminal, execute:

	cat /etc/schmooze/pbx-version

The FreePBX Distro release identifier is the final number. For example, you previously deployed the following script: <code>4.211.64-1-Installer-Script.sh</code>. Notice the final number: <code>1</code> in this example.

### Download & Execute Applicable Upgrade Script

To execute an update script, enter the following commands in a terminal window:

	cd
	mkdir ~/FreePBX/UpdateScripts
	cd ~/FreePBX/UpdateScripts

**Upgrade Step 1:** Execute, in a terminal window:

	wget http://upgrades.freepbxdistro.org/stable/4.211.64/upgrade-4.211.64-2.sh

**Upgrade Step 2:** Make the file executable:

	chmod +x upgrade-4.211.64-2.sh

**Upgrade Step 3:** Execute:

	./upgrade-4.211.64-2.sh

The Distro upgrade script will update both FreePBX components (Asterisk & the FreePBX web GUI) and all base CentOS components.

**Upgrade Step 4:** Specific Script Instructions

Note or carry out any special instructions displayed (if any) at the end of the upgrade, such as to reboot the system.

**Upgrade Step 5:** Confirm New FreePBX Distro Version

Check the updated (now current) version file (as demonstrated above) to confirm the current installed version of FreePBX Distro is the expected newer version.

Repeat <code>Upgrade Steps 1-5</code> for each individual update script, in sequential order:

	http://upgrades.freepbxdistro.org/stable/4.211.64/upgrade-4.211.64-3.sh
	http://upgrades.freepbxdistro.org/stable/4.211.64/upgrade-4.211.64-4.sh
	http://upgrades.freepbxdistro.org/stable/4.211.64/upgrade-4.211.64-5.sh
	http://upgrades.freepbxdistro.org/stable/4.211.64/upgrade-4.211.64-6.sh
	http://upgrades.freepbxdistro.org/stable/4.211.64/upgrade-4.211.64-7.sh
	http://upgrades.freepbxdistro.org/stable/4.211.64/upgrade-4.211.64-8.sh

### Confirm Successful Installation

To confirm that FreePBX & Asterisk were installed successfully, open a web browser, navigate to your cloud server's FQDN, and you should be greeted by the FreePBX administrator-account setup screen.

![FreePBX Account Setup](http://i.imgur.com/AQPW20I.png)

### Create admin user & the admin password

That username and password will be used in the future to access the FreePBX configuration screen.

**Note:** These passwords do not change the Root password! They are only used for access to the FreePBX web interface.

The main FreePBX screen will offer you four options:

## Security

Any server accessible from the public Internet should be security hardened, and an Asterisk IP-PBX is no exception. Security best practices are not within the scope of this article. However, at a minimum, you should review and deploy the following security measures:

* Fail2Ban was automatically installed, but still needs to be configured from the System Administration module; or, in the alternative, commence at **Step Two** of [How To Protect SSH with fail2ban on CentOS 6](https://www.digitalocean.com/community/articles/how-to-protect-ssh-with-fail2ban-on-centos-6);
* [How To Install DenyHosts on CentOS 6](https://www.digitalocean.com/community/articles/how-to-install-denyhosts-on-centos-6); and
* [How To Setup a Basic IP Tables Configuration on Centos 6](https://www.digitalocean.com/community/articles/how-to-setup-a-basic-ip-tables-configuration-on-centos-6), and open the following ports, for the initial setup and testing phase:
	*	1720 (TCP)
	*	2000 (TCP)
	*	2727 (UDP)
	*	4520 (UDP)
	*	4569 (UDP)
	*	5000 (UDP)
	*	5038 (TCP)
	*	5060 (TCP & UDP)
	*	5061 (TCP)
	*	10000-20000 (UDP)

**Note:** Remember to close any unused ports once you deploy your Asterisk server into production.

## Configure Mail Server

Configure Postfix, beginning with **Step Two** of this article: [How To Install Postfix on CentOS 6 | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-install-postfix-on-centos-6).

## Additional Configuration

To continue configuring Asterisk, check out the [FreePBX Distro First Steps After Installation](http://wiki.freepbx.org/display/FD/FreePBX+Distro+First+Steps+After+Installation) or take a look at Asterisk official [online training](http://www.digium.com/en/training/asterisk/essentials/free-videos).

## Additional Resources

* [Asterisk Wiki](https://wiki.asterisk.org/wiki/dashboard.action)
* [Official Asterisk site](http://www.asterisk.org/)
* [Asterisk Support Forums](http://forums.asterisk.org/)
* [Asterisk Internet Relay Chat (IRC)](http://www.asterisk.org/community/discuss)

<p><div style="text-align: right; font-size:smaller;">Article submitted by: <a href="https://plus.google.com/107285164064863645881?rel=author" target="_blank">Pablo Carranza</a> &bull; DATE</div></p>

# ~~~~~~~~~~ DRAFT ~~~~~~~~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls) gladly accepted*