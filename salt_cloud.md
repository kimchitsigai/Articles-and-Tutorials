Automated Provisioning of DigitalOcean Cloud Servers with Salt Cloud
=====

### Introduction

Salt Cloud is a public cloud provisioning tool. It integrates [SaltStack](http://saltstack.com/) with DigitalOcean's [application programming interface (API)](https://www.digitalocean.com/api/) in a clean way, so that DigitalOcean droplets (i.e. cloud servers or VPS) can be quickly and easily modeled and provisioned.

## Benefits

Salt Cloud allows for cloud-based servers to be managed via virtual machine maps and profiles. This means that DigitalOcean droplets can be created individually, or large groups of systems can be managed at once. Droplets created with Salt Cloud have Salt automatically installed and are assigned to a specified *master*. This means that droplets can be provisioned and potentially never need to be logged into. 

## Prerequisites

Any server accessible from the public Internet should be security hardened.

#### Server Hardening

*	Changed your SSH port from the default Port 22 to a random port, as described in **Step Five** of [Initial Server Setup with Ubuntu 12.04](https://www.digitalocean.com/community/articles/initial-server-setup-with-ubuntu-12-04);
*	Configure a [firewall](https://www.digitalocean.com/community/articles/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server) and make sure to open your SSH port and ports 4505 & 4506;
*	Deploy [Fail2ban](https://www.digitalocean.com/community/articles/how-to-protect-ssh-with-fail2ban-on-ubuntu-12-04) and [DenyHosts](https://www.digitalocean.com/community/articles/how-to-install-denyhosts-on-ubuntu-12-04).

#### Create Salt Master/minion

This article assumes that you followed the steps in [How To Install Salt on Ubuntu 12.04](https://www.digitalocean.com/community/articles/how-to-install-salt-on-ubuntu-12-04) to create a Salt master & minion (on the same host):

*	Make a note of your Salt master's Fully Qualified Domain Name (FQDN)

#### Create SSH Key Pair

Then,:

	mkdir /keys

Follow the steps in [How To Use SSH Keys with DigitalOcean Droplets](https://www.digitalocean.com/community/articles/how-to-use-ssh-keys-with-digitalocean-droplets) to create an SSH key pair (with no passphrase) in the following directory & file:

	/keys/digital-ocean-salt-cloud

Next, ececute:

	cat /keys/digital-ocean-salt-cloud.pub

Make sure to copy the SSH public key in your DigitalOcean Control Panel, as outlined in **Step Three** of [How To Use SSH Keys with DigitalOcean Droplets](https://www.digitalocean.com/community/articles/how-to-use-ssh-keys-with-digitalocean-droplets).

## Install Salt-Cloud Dependencies

First:

	apt-get -y install python-pip git-core

Then,:

	pip install pyzmq PyYAML pycrypto msgpack-python jinja2 psutil salt

Next,:

	pip install apache-libcloud

## Install Salt-Cloud

Finally,:

	pip install git+https://github.com/saltstack/salt-cloud.git#egg=salt_cloud

Verify it was successfully installed:

	salt-cloud --version

## Configure Salt-Cloud

Salt-cloud uses two YAML files for configuration:

	vim /etc/salt/cloud

This is the main configuration file. Replace `master.yourdomain.tld` with the FQDN of your Salt master:

	provider: do
	minion:
	    master: master.yourdomain.tld
	    append_domain: yourdomain.tld

Create an API key by following the steps outlined in [How To Use the DigitalOcean API](https://www.digitalocean.com/community/articles/how-to-use-the-digitalocean-api). Then,

	vim /etc/salt/cloud.providers

This is the provider configuration file. We will create the do (Digital Ocean) provider in this file. Collect your client_key and api_key from the user dashboard. Also create an SSH key and add the public key using the dashboard:

	# For DigitalOcean
	do:
	  provider: digital_ocean
	  client_key: MyClientKeyCopiedFromDashboard
	  api_key: MyAPIKeyCopiedFromDashboard
	  ssh_key_file: /keys/digital-ocean-salt-cloud
	  ssh_key_name: digital-ocean-salt-cloud.pub

Next,

	vim /etc/salt/cloud.profiles

This is the profiles configuration file. We will create just one profile for now, but you can create unlimited named combinations.

	ubuntu_do:
	  provider: do
	  image: Ubuntu 12.04 x64
	  size: 512MB
	  script: Ubuntu
	  location: New York 2

After you configure the do provider in /etc/salt/cloud.providers you gain access to the following commands:

	salt-cloud --list-sizes do
	salt-cloud --list-locations do
	salt-cloud --list-images do
	salt-cloud --help

## Provision a New Cloud Server!

Replace `hostname`.

	salt-cloud --profile ubuntu_do hostname

If all goes well you should have a newly provisioned server bootstrapped with salt-minion. The new minion’s keys are already added to the salt-master. Now you just need to run [highstate](http://docs.saltstack.com/ref/states/highstate.html)!

## Additional Resources

* [How To Create Your First Salt Formula | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-create-your-first-salt-formula);
* [Highstate Data Structure Definitions | SaltStack Docs](http://docs.saltstack.com/ref/states/highstate.html);
* [Link3]().

As always, if you need help with the steps outlined in this HowTo, look to the DigitalOcean Community for assistance by posing your question(s), below.

<p><div style="text-align: right; font-size:smaller;">Article submitted by: <a href="https://plus.google.com/21-digit_Google+_Profile_ID_number?rel=author" target="_blank">Pablo Carranza</a> &bull; DATE</div></p>
