# ~~~~~~~~~~ DRAFT ~~~~~~~~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls) gladly accepted*

Deploy a VoIP Private Branch Exchange (PBX) on Ubuntu Server
====

### Introduction

Since the introduction of the private branch exchange (PBX) in the 1970s, companies have become dependent on the many features that were introduced over time. Today, businesses have a variety of options when it comes to telephone systems. Many are choosing the cloud with a hosted PBX solution in order to reduce capital expenses, maintenance & upgrade costs &ndash; while still reaping the benefits of the latest PBX features.

## What is a PBX?

A PBX is a system that connects internal telephone extensions to outside public & mobile telephone networks. An IP (Internet Protocol) PBX is a PBX that provides audio, video, and instant messaging communication through data networks connected with the Public Switched Telephone Network (PSTN) for worldwide telephony communication.

In addition to the traditional PSTN (i.e., copper wire), modern PBX systems also utilize the Voice over Internet Protocol (VoIP). VoIP gateways enable businesses to use their managed intranets to help reduce long-distance expenses; and enjoy the benefits of a single network for voice and data communication.

## Asterisk IP-PBX

Asterisk is an open-source software implementation of a telephone PBX. Like any PBX, it allows connected telephones to make calls to one another, and to connect to other telephone services, such as traditional landlines (i.e., PSTN) and VoIP services.

The Asterisk software includes many features available in proprietary PBX systems: voice mail, conference calling, interactive voice response (phone menus), and automatic call distribution. In addition, users can create new functionality by writing dial plan scripts in several of Asterisk's own extensions languages or by adding custom, loadable modules.

## Internationalization

Asterisk has become a popular IP-PBX worldwide due to its quality and flexibility. The American-English, French, Persian (i.e., Farsi) and Mexican-Spanish female voices for the Interactive voice response and voice mail features of Asterisk are frequently updated. Many other languages and dialects are also available. Additionally, different voice sets are offered for commercial sale in different languages, dialects and genders.

## Preparatory Steps

First, you must decide on what server you wish to deploy your cloud PBX.

#### Location

Voice quality on VoIP calls is affected by network latency. Thus, it is advisable to select a datacenter that is closest to the general, geographic location of the majority of your anticipated calls.

#### Server Specifications

Deciding on the server's type and size is not an exact science. Try to best anticipate your average call volume; and then follow the steps outlined in: [How To Create Your First DigitalOcean Droplet Virtual Server](https://www.digitalocean.com/community/articles/how-to-create-your-first-digitalocean-droplet-virtual-server).

Generally speaking, a:

*  512 MB droplet supports approximately `5-10` concurrent calls;
*  1 GB droplet supports approx. `11-24` concurrent calls;
*  2 GB droplet supports approx. `25-50` concurrent calls;
*  4 GB droplet supports approx. `51-100` concurrent calls;
*  8 GB droplet supports approx. `101-200` concurrent calls;
*  16 GB droplet supports approx. `201+` concurrent calls;

#### Operating System (OS) 

This guide assumes, `Ubuntu Server 12.04 LTS 64-bit ` is chosen.

#### Initial Setup

Next, complete the [Initial Server Setup with Ubuntu 12.04](https://www.digitalocean.com/community/articles/initial-server-setup-with-ubuntu-12-04) guide.

#### Setting the Timezone

You can set your server's timezone to whatever you want. It may be best to set it to the same timezone of most of your callers. If you are unsure which timezone would be best, consider using universal coordinated time (UTC) &ndash; also known as `Greenwich Mean Time`. Execute the following command to access the timezone utility:

	sudo dpkg-reconfigure tzdata

#### Update `/etc/hosts`

Next, execute (obviously, you can use whichever text editor you wish):

	vim /etc/hosts

(This guide assumes that you have installed the [vim text editor](https://www.digitalocean.com/community/articles/installing-and-using-the-vim-text-editor-on-a-cloud-server).) Then, tap on the `i` key and use the arrow keys on your keyboard to navigate the text area. Edit the `hosts` file to resemble the example, below &ndash; replacing `host` with your chosen hostname and `yourdomain.tld` with your `Fully Qualified Domain Name (FQDN)`.

	127.0.0.1	localhost.localdomain	localhost
	127.0.1.1	host.yourdomain.tld		host
	12.34.56.78	host.yourdomain.tld		host

To save & exit, tap the `Esc` key; then, the `:` key; followed by the `w` key; the `q` key; and, finally, `Enter`.

**Note:** The value you assign as your system's FQDN should have an `A record` in DNS pointing to your droplet's IPv4 address. *See* [How To Set Up a Host Name with DigitalOcean](https://www.digitalocean.com/community/articles/how-to-set-up-a-host-name-with-digitalocean).

#### DNS

If you plan on using Asterisk's email features, you may also wish to add an [MX record](https://www.digitalocean.com/community/articles/how-to-set-up-a-host-name-with-digitalocean) for your domain and have a mail server (such as `Exim` send-only mail server) installed:

	sudo apt-get -y install exim4

Then, execute:

	sudo dpkg-reconfigure exim4-config

and configure everything according to your needs.

#### Install Software Updates

Now you need to install the available software updates for your Ubuntu server. Doing so patches security holes in packages and helps protect your droplet against unauthorized access.

	sudo apt-get update && sudo apt-get -y upgrade && sudo apt-get -y dist-upgrade && sudo apt-get -y autoremove && sudo reboot now

When the update completes, the server will reboot.

## Installation

Asterisk and the majority of it dependencies are now available via Ubuntu's official repositories; and can easily be installed by executing the following command:

	sudo apt-get -y install asterisk

During the automatic installation, you will be presented the following screen:

![Asterisk will ask you to enter your ITU-T telephone code](./images/asterisk_country_code.png)

If you are going to be placing the majority of your calls within North America, enter the number `1` and then tap on the `Tab` key (to highlight the <Ok> option) and, finally, tap the `Enter` key. Click here to confirm your country's [calling code](http://en.wikipedia.org/wiki/List_of_country_calling_codes).

#### Confirm

To confirm that Asterisk was installed successfully, execute the following command:

	sudo asterisk -r

which will launch the Asterisk Command Line Interface (CLI) &ndash; from which Asterisk can be configured.

To logout of the Asterisk CLI, simply execute:

	exit

## Security

Any server accessible from the public Internet should be security hardened, and an Asterisk IP-PBX is no exception. Security best practices are not within the scope of this article. However, at a minimum, you should review and deploy the following security measures:

* [How To Protect SSH with fail2ban on Ubuntu 12.04](https://www.digitalocean.com/community/articles/how-to-protect-ssh-with-fail2ban-on-ubuntu-12-04);
* [How To Install DenyHosts on Ubuntu 12.04](https://www.digitalocean.com/community/articles/how-to-install-denyhosts-on-ubuntu-12-04); and
* [How to Setup a Firewall with UFW on an Ubuntu and Debian Cloud Server](https://www.digitalocean.com/community/articles/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server), and open the following ports, for the initial setup and testing phase:
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

## Configuration

To continue configuring Asterisk, check out the [Quick Start Guide](http://www.asterisk.org/sites/asterisk/files/mce_files/documents/asterisk_quick_start_guide.pdf) or take a look at Asterisk official [online training](http://www.digium.com/en/training/asterisk/essentials/free-videos).

## Additional Resources

* [Asterisk Wiki](https://wiki.asterisk.org/wiki/dashboard.action)
* [Official Asterisk site](http://www.asterisk.org/)
* [Asterisk Support Forums](http://forums.asterisk.org/)
* [Asterisk Internet Relay Chat (IRC)](http://www.asterisk.org/community/discuss)

<p><div style="text-align: right; font-size:smaller;">Article submitted by: <a href="https://plus.google.com/107285164064863645881?rel=author" target="_blank">Pablo Carranza</a></div></p>

# ~~~~~~~~~~ DRAFT ~~~~~~~~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls) gladly accepted* 
