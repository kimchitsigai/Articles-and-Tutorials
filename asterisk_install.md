# ~~~~~~~~~~ DRAFT ~~~~~~~~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls) gladly accepted*

Deploy a VoIP Private Branch Exchange (PBX) on CentOS 6.4
====

### Introduction

Since the introduction of the private branch exchange (PBX) in the 1970s, companies have become dependent on the many features that were introduced over time. Today, businesses have a variety of options when it comes to telephone systems. Many are choosing the cloud with a hosted PBX solution in order to reduce capital expenses, maintenance & upgrade costs &ndash; while still reaping the benefits of the latest PBX features.

## What is a PBX?

A PBX is a system that connects internal telephone extensions to outside public & mobile telephone networks. An IP (Internet Protocol) PBX is a PBX that provides audio, video, and instant messaging communication through data networks connected with the Public Switched Telephone Network (PSTN) for worldwide telephony communication.

In addition to the traditional PSTN (i.e. copper wire), modern PBX systems also utilize the Voice over Internet Protocol (VoIP). VoIP gateways enable businesses to use their managed intranets to help reduce long-distance expenses; and enjoy the benefits of a single network for voice and data communication.

## Asterisk IP-PBX

Asterisk is an open-source software implementation of a telephone PBX. Like any PBX, it allows connected telephones to make calls to one another, and to connect to other telephone services, such as traditional landlines (i.e. PSTN) and VoIP services.

The Asterisk software includes many features available in proprietary PBX systems: voice mail, conference calling, interactive voice response (phone menus), and automatic call distribution. In addition, users can create new functionality by writing dial-plan scripts in several of Asterisk's own extensions languages or by adding custom, loadable modules.

## Preparatory Steps

First, you must make a few structural decisions, before you deploy your cloud PBX.

### Location

Voice quality on VoIP calls is affected by network latency, among other factors. Thus, it is advisable to select a datacenter that is closest to the general, geographic location of the majority of your anticipated calls.

### Server Specifications

Deciding on the best size for your cloud IP-PBX server is not an exact science. Try to best anticipate your average call volume; and then follow the steps outlined in: [How To Create Your First DigitalOcean Droplet Virtual Server](https://www.digitalocean.com/community/articles/how-to-create-your-first-digitalocean-droplet-virtual-server) to deploy a `64-bit CentOS 6.4` VPS.

>#### SSH Keys
>
For increased security, it's advisable that you create your droplet with pre-installed SSH keys. *See* [How To Use SSH Keys with DigitalOcean Droplets](https://www.digitalocean.com/community/articles/how-to-use-ssh-keys-with-digitalocean-droplets). Windows users may consult [How To Create SSH Keys with PuTTY to Connect to a VPS](https://www.digitalocean.com/community/articles/how-to-create-ssh-keys-with-putty-to-connect-to-a-vps).

Generally speaking, a:

*  512 MB droplet supports approximately `5-15` concurrent calls;
*  1 GB droplet supports approx. `15-25` concurrent calls;
*  2 GB droplet supports approx. `25-50` concurrent calls;
*  4 GB droplet supports approx. `50-100` concurrent calls;
*  8 GB droplet supports approx. `100-175` concurrent calls;
*  16 GB droplet supports approx. `175+` concurrent calls;

**NOTE:** It is safe to "guess low." If you underestimate your call volume, try adding swap space. *See* [How To Add Swap on CentOS 6](https://www.digitalocean.com/community/articles/how-to-add-swap-on-centos-6). In addition, DigitalOcean makes it easy to resize your cloud server. *See* [How To Resize Droplets Using Snapshots](https://www.digitalocean.com/community/articles/how-to-resize-droplets-using-snapshots).

### Setting the Timezone

You can set your server's timezone to whatever you want. It may be best to set it to the same timezone as most of your callers. If you are unsure which timezone would be best, consider using universal coordinated time (UTC) &ndash; also known as `Greenwich Mean Time`.

To view the current timezone, execute:

	date

To change your CentOS server's timezone, you need to create a symbolic link from a zone file in `/usr/share/zoneinfo` to `/etc/localtime`. In doing so, you first need to find the zone file for your timezone (see the examples below for common possibilities):

#### Universal Coordinated Time:

	ln -sf /usr/share/zoneinfo/UTC /etc/localtime 

#### Eastern Standard Time:

	ln -sf /usr/share/zoneinfo/EST /etc/localtime

#### American Central

	ln -sf /usr/share/zoneinfo/US/Central /etc/localtime 

#### American Eastern

	ln -sf /usr/share/zoneinfo/US/Eastern /etc/localtime

### Update Current Software

Now you need to install the available software updates for your CentOS server. Doing so patches security holes in packages and helps protect your droplet against unauthorized access.

	yum update

## Installation

First, navigate to your server's `/tmp` directory by executing the following command:

	cd /tmp

Next, download an installation script by executing:

	wget http://upgrades.freepbxdistro.org/blank-centos-installer/4.211.64-track/4.211.64-1-Installer-Script.sh

Then, make the script executable:

	chmod +x 4.211.64-1-Installer-Script.sh

Run the installer script, by executing:

	./4.211.64-1-Installer-Script.sh

Once completed you should have a full functioning  IP-PBX FreePBX Distro. 

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