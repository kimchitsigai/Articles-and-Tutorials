Automated Provisioning of DigitalOcean Cloud Servers with Salt Cloud on Ubuntu 12.04
=====

### Introduction

SaltStack is an open-source cloud deployment, configuration management, remote execution & monitoring package. You may not have heard of [SaltStack](http://saltstack.org), but you might be familiar with [Puppet](https://www.digitalocean.com/community/articles/how-to-install-puppet-on-a-digitalocean-vps) or [Chef](https://www.digitalocean.com/community/articles/how-to-install-chef-and-ruby-with-rvm-on-a-ubuntu-vps). Salt is a similar tool, but it is relatively lightweight as far as resources and requirements and a growing number of users consider it much easier to use.

This tutorial aims to provide the reader with a simplified, basic setup of an operable Salt Cloud master server; which DigitalOcean users can employ to automate the process of creating 1, 2 or a fleet of cloud servers.

## Infrastructure Management

Tools like Salt, Puppet and Chef allow you to issue commands on multiple machines at once; and also install and configure software. Salt has two main aspects: configuration management and remote execution; while cloud deployment is left to Salt Cloud.

#### Salt Master & Salt Minion(s)

The terms [master](http://docs.saltstack.com/topics/configuration.html#term-master) and [minion](http://docs.saltstack.com/topics/configuration.html#term-minion) refer to the controller and the controlled. The master is essentially the central coordinator for all of the minions &ndash; similar to a client/server configuration where the master is the server, and the minion is the client.

## Framework

Salt Cloud is a public-cloud provisioning tool designed to automate the
deployment of public-cloud servers. It integrates SaltStack with DigitalOcean's [application programming interface (API)](https://www.digitalocean.com/api/) in a clean way; and manages cloud-based servers via virtual machine [maps](https://salt-cloud.readthedocs.org/en/latest/topics/map.html) and [profiles](http://salt-cloud.readthedocs.org/en/latest/topics/profiles.html).

DigitalOcean droplets (i.e. cloud servers or virtual private servers)  created with Salt Cloud have Salt automatically installed and are assigned to a specified *master*. As previously mentioned, the Salt master is the central server that all *minions* (i.e., client servers) connect to. Commands are executed on the minions through the master, and minions send data back to the master (unless otherwise redirected with a [returner](http://docs.saltstack.com/ref/returners/index.html)).

## Benefits

DigitalOcean droplets can be created individually or in large groups and
can be provisioned and fully managed, without ever needing to be logged into. From deploying single virtual machines, to mapping and provisioning entire clouds, Salt Cloud is as scaleable as you need it to be.

## Prerequisites

First, consult [How To Install Salt on Ubuntu 12.04 | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-install-salt-on-ubuntu-12-04) and create a Salt master/minion.

#### Security Hardening

Any server accessible from the public Internet should be security hardened, and your Salt master is no exception:

* Change your SSH port from the default Port 22 to a random port **below 1024**, as described in **Step Five** of [Initial Server Setup with Ubuntu 12.04](https://www.digitalocean.com/community/articles/initial-server-setup-with-ubuntu-12-04);
* Configure a [firewall](https://www.digitalocean.com/community/articles/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server) and make sure to open your **custom SSH port** and **TCP Ports 4505 & 4506**;
* Either [disable password logins](https://www.digitalocean.com/community/articles/how-to-create-ssh-keys-with-putty-to-connect-to-a-vps) or deploy [Fail2ban](https://www.digitalocean.com/community/articles/how-to-protect-ssh-with-fail2ban-on-ubuntu-12-04) & [DenyHosts](https://www.digitalocean.com/community/articles/how-to-install-denyhosts-on-ubuntu-12-04).

#### Create SSH Key Pair for DigitalOcean Control Panel

Salt Cloud uses public key encryption to secure the communication between  the Salt master and DigitalOcean. Therefore, create a directory in which to store your SSH keys:

	sudo mkdir /keys

Next, execute:

	sudo ssh-keygen -t rsa

The system will respond with <code>Enter file in which to save the key (/root/.ssh/id_rsa):</code>. Copy & paste:

	/keys/digital-ocean-salt-cloud

and press <code>Enter</code>. The system will next display <code>Enter passphrase (empty for no passphrase):</code>, asking you to enter an *optional* password. **Do not** enter a passphrase; and, instead, tap the <code>Enter</code> key twice.

Your new public (SSH) key is now located in <code>/keys/digital-ocean-salt-cloud.pub</code>. Finally, execute:

	cat /keys/digital-ocean-salt-cloud.pub

and copy & paste the public key into your DigitalOcean Control Panel, as outlined in **Step Three** of [How To Use SSH Keys with DigitalOcean Droplets](https://www.digitalocean.com/community/articles/how-to-use-ssh-keys-with-digitalocean-droplets) (but save the name of the public key, in your Control Panel, as <code>digital-ocean-salt-cloud.pub</code>).

#### Hostname & Fully Qualified Domain Name (FQDN)

Verify that your Salt master's hostname and FQDN are properly set. *See* [Setting the Hostname & Fully Qualified Domain Name (FQDN) on Ubuntu Server 12.04 LTS](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/blob/master/set_hostname_fqdn_on_ubuntu.md#setting-the-hostname--fully-qualified-domain-name-fqdn-on-ubuntu-server-1204-lts).

#### Acquire Installation Tools

SaltStack is built with the Python programming language; so, we'll need <code>pip</code> (a package management system used to install and manage software packages written in Python). Despite being available via Python's repositories, we'll be downloading the Salt Cloud package from SaltStack's GitHub [repository](https://github.com/saltstack/salt-cloud/).

To install <code>pip</code> and <code>git</code> on your system, execute:

	sudo apt-get -y install python-pip git-core

## Install Salt Cloud Dependencies

First, execute:

	sudo apt-get -y install python-m2crypto

Then, execute:

	sudo pip install pyzmq PyYAML pycrypto msgpack-python jinja2 psutil salt

Next, execute:

	sudo pip install apache-libcloud

## Install Salt Cloud

Finally, execute:

	sudo pip install git+https://github.com/saltstack/salt-cloud.git#egg=salt_cloud

Verify Salt Cloud was successfully installed, by executing:

	salt-cloud --version

## Configure Salt Cloud

Salt Cloud runs on a module system similar to the main Salt project. The core configuration of Salt cloud is handled in the [cloud configuration file](https://salt-cloud.readthedocs.org/en/latest/topics/config.html). This file is comprised of global configurations for interfacing with various cloud providers.

Create the cloud configuration file by executing (obviously, you can use whichever text editor you wish; but this guide assumes that you have installed the [vim text editor](https://www.digitalocean.com/community/articles/installing-and-using-the-vim-text-editor-on-a-cloud-server)):

	sudo vim /etc/salt/cloud

Now, on your keyboard, tap on the <code>i</code> key and use the arrow keys to navigate the text area and create your `cloud` file so that it resembles the example, below (replacing <code>master.yourdomain.tld</code> with the FQDN of your Salt master):

	provider: do
	# Set the location of the Salt master
	minion:
	    master: master.yourdomain.tld
	    append_domain: yourdomain.tld

To save & exit, tap the <code>Esc</code> key; then, the <code>:</code> key; followed by the <code>w</code> key; the <code>q</code> key; and, finally, <code>Enter</code>.

### Cloud Provider Modules

Next, create two new directories:

	sudo mkdir /etc/salt/{cloud.profiles.d,cloud.providers.d}

These new directories will hold the DigitalOcean YAML configuration files.

### DigitalOcean Cloud Configuration

Create the DigitalOcean provider configuration file by executing:

	sudo vim /etc/salt/cloud.providers.d/digital_ocean.conf

Tap on the <code>i</code> key and use the arrow keys on your keyboard to navigate the text area and create your `digital_ocean.conf` file so that it resembles the example, below:

	do:
	  provider: digital_ocean
	  # Digital Ocean account keys
	  client_key: YourClientIDCopiedFromControlPanel
	  api_key: YourAPIKeyCopiedFromControlPanel
	  ssh_key_name: digital-ocean-salt-cloud.pub
	  # Directory on your Salt master
	  ssh_key_file: /keys/digital-ocean-salt-cloud

### DigitalOcean API

Using Salt Cloud with DigitalOcean requires a <code>client_key</code> and an <code>api_key</code>. These can be found in the [DigitalOcean Control Panel](https://www.digitalocean.com/community/articles/the-digitalocean-control-panel), under the API Access tab.

Now, create an API key for your account by following the steps outlined in [How To Use the DigitalOcean API](https://www.digitalocean.com/community/articles/how-to-use-the-digitalocean-api). You will need to copy & paste both your DigitalOcean <code>Client ID</code> and <code>API Key</code> in the configuration file described, above.

### Interacting with the API

After you configure the DigitalOcean provider in <code>/etc/salt/cloud.providers.d/digital_ocean.conf</code>, you gain access to the following commands:

	sudo salt-cloud --list-images do
	sudo salt-cloud --list-sizes do
	sudo salt-cloud --list-locations do
	sudo salt-cloud --help

The output from these commands are important because it provides the variables needed to build our virtual machine profiles.

### DigitalOcean Cloud Profile

The DigitalOcean cloud profile is used to control access to the DigitalOcean VPS system. Create the DigitalOcean cloud [profiles](https://salt-cloud.readthedocs.org/en/latest/topics/profiles.html) for your server fleet, by executing:

	sudo vim /etc/salt/cloud.profiles.d/digital_ocean.conf

Now, on your keyboard, tap on the <code>i</code> key and use the arrow keys to navigate the text area and create your `digital_ocean.conf` file so that it resembles the example, below:

	# Official distro images available for Arch, CentOS, Debian, Fedora, Ubuntu

	ubuntu_512MB_ny2:
	  provider: do
	  image: Ubuntu 12.04 x64
	  size: 512MB
	  script: Ubuntu
	  location: New York 2

	ubuntu_1GB_ny2:
	  provider: do
	  image: Ubuntu 12.04 x64
	  size: 1GB
	  script: Ubuntu
	  location: New York 2

	ubuntu_2GB_ny2:
	  provider: do
	  image: Ubuntu 12.04 x64
	  size: 2GB
	  script: Ubuntu
	  location: New York 2

	# Create additional profiles, if you wish
	[profile_name_of_your_choosing]
	  provider: do
	  image: [from salt-cloud --list-images do]
	  size: [from salt-cloud --list-sizes do]
	  script: [optional deployment script e.g. Ubuntu, Fedora, python-bootstrap, etc.]
	  location: [from salt-cloud --list-locations do]

## Provision a New Cloud Server!

To create a new cloud server, execute (replacing <code>hostname</code> with any hostname of your choice):

	sudo salt-cloud --profile ubuntu_512MB_ny2 hostname

If all goes well you should have a newly-provisioned server, bootstrapped with Salt minion (the new minion's SSH keys will automatically be added to the Salt master). If you would like to provision multiple virtual machines from the same profile, you can do so with a single command, e.g.

	sudo salt-cloud --profile ubuntu_1GB_ny2 hostname1 hostname2 hostname3

## Minion Configuration

To configure your new fleet, consult: [How To Create Your First Salt Formula | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-create-your-first-salt-formula).

## Destroy a Minion

## Additional Resources

* [Salt Cloud Documentation](https://salt-cloud.readthedocs.org/en/latest/index.html);
* [Frequently Asked Questions | SaltStack](http://docs.saltstack.com/faq.html);
* All DigitalOcean [Configuration Management](https://www.digitalocean.com/community/community_tags/configuration-management) articles.

As always, if you need help with the steps outlined in this HowTo, look to the DigitalOcean Community for assistance by posing your question(s), below.

<p><div style="text-align: right; font-size:smaller;">Article submitted by: <a href="https://plus.google.com/107285164064863645881?rel=author" target="_blank">Pablo Carranza</a> &bull; DATE</div></p>