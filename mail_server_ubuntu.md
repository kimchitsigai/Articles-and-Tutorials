#### ~~~ WIP ~ Draft ~ WIP ~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls)* gladly accepted
How to Deploy a Dovecot-Postfix Mail Server on Ubuntu 12.04
=

### Introduction

There are many, many ways in which to setup and configure a mail server. This article aims to outline a quick, and simple, way of deploying a mail server with the Postfix and Dovecot backends.

## Server Setup

Follow the steps outlined in:

1. [How To Create Your First DigitalOcean Droplet Virtual Server](https://www.digitalocean.com/community/articles/how-to-create-your-first-digitalocean-droplet-virtual-server)

	>For increased security, it is advisable that you create your droplet with pre-installed SSH keys. *See* [How To Use SSH Keys with DigitalOcean Droplets](https://www.digitalocean.com/community/articles/how-to-use-ssh-keys-with-digitalocean-droplets). **Windows users:** Refer to [How To Create SSH Keys with PuTTY to Connect to a VPS | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-create-ssh-keys-with-putty-to-connect-to-a-vps)
2. [Initial Server Setup with Ubuntu 12.04 | DigitalOcean](https://www.digitalocean.com/community/articles/initial-server-setup-with-ubuntu-12-04).
	>* For added security, it is advisable that you disable password logins. *See* [How To Create SSH Keys with PuTTY to Connect to a VPS | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-create-ssh-keys-with-putty-to-connect-to-a-vps).

#### Hostname & FQDN

Set your server's hostname and Fully Qualified Domain Name by implementing the steps in [Setting the Hostname & Fully Qualified Domain Name (FQDN) on Ubuntu 12.04 or CentOS 6.4](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/blob/master/set_hostname_fqdn_on_ubuntu_centos.md).

#### Timezone

You can change your server's timezone to whatever you want; altough it may be best to set it to the same timezone of most of your users. To do so, simply execute, in a terminal:

    sudo dpkg-reconfigure tzdata

and follow the instructions in the ensuing, on-screen prompts.

## Update Your System

To make sure that your server operating system (OS) is up to date, execute:

	sudo apt-get update && sudo apt-get -y dist-upgrade && sudo apt-get -y autoremove && sudo reboot now

When the update completes, the server will reboot to make sure that all of the software upgrades take effect.

## Install Mail Server Packages

The <code>mail-stack-delivery</code> package will install Dovecot and configure Postfix to use it for both SASL authentication and as a Mail Delivery Agent (MDA). The package also configures Dovecot for IMAP, IMAPS, POP3, and POP3S. To install the package, execute:

	sudo apt-get -y install mail-stack-delivery

### Configuration Options

1. During the installation, you will presented a prompt, asking you to "select the mail server configuration type that best meets your needs." Leave the default option set (<code>Internet Site</code>), tap on the <code>Tab</code> key, on your keyboard, to highlight <code>&lt;Ok></code>, and tap on the <code>Enter</code> key.

2. You will then be asked to provide the Fully Qualified Domain Name (FQDN) of your mail server. If you configured your <code>/etc/hosts</code> file correctly, your servers FQDN should be prefilled. Tap on the <code>Tab</code> key, on your keyboard, to highlight <code>&lt;Ok></code>, and tap on the <code>Enter</code> key.

Congratulations, you now have a working mail server.

**Note:** The <code>mail-stack-delivery</code> package uses the certificate and key from the <code>ssl-cert</code> package and is more than adequate for testing purposes.

## SSL Certificate

To use your new mail server in a production environment, you should create a custom SSL certificate and key generated specifically for your mail server. You can obtain a free SSL certificate from the StartCom Certification Authority (CA), at [StartSSL.com](http://www.startssl.com/) or you may create a self-signed certificate.

#### Generating a Certificate Signing Request (CSR)

To obtain an SSL certificate from a commercial CA, you will need to provide the CA with a CSR. To generate a CSR, execute:

	sudo openssl req -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/mail.key -out mail.csr

### Self-signed Certificate

To create a self-signed certificate, execute:

	sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/mail.key -out /etc/ssl/certs/mail.pem

### Add Certificate Details to the Postfix  &amp; Dovecot Configuration Files

Once you have a customized certificate and key for your mail server, execute:

	sudo vim /etc/postfix/main.cf

and change the following directives to reflect the locations of your certificate and private key:

	smtpd_tls_cert_file = /etc/ssl/certs/mail.pem
	smtpd_tls_key_file = /etc/ssl/private/mail.key

Then, do the same for Dovecot:

	sudo vim /etc/dovecot/conf.d/01-mail-stack-delivery.conf 

Finally, restart Postfix:

	sudo /etc/init.d/postfix restart

## Limiting Mail Delivery to Secure Protocols

Now that you have the benefits of SSL security available, make sure that secure protocols are always used:

	sudo vim /etc/dovecot/dovecot.conf

Look for the <code>protocols</code> directive and limit the protocols to the following:

	protocols = imaps pop3s

Then, execute:

	sudo vim /etc/dovecot/conf.d/01-mail-stack-delivery.conf

and do the same:

	protocols = imaps pop3s

In addition, uncomment the <code>inet_listener imaps</code> section by removing the <code>#</code> character, i.e.

	inet_listener imaps

## Limit Sending Mail to Secure Protocol

Execute:

	sudo vim /etc/postfix/master.cf

Uncomment the <code>smtps inet</code> line and all the <code>-o</code> options that follow.

## Security

Any server accessible from the public Internet should be security hardened, and a mail server is no exception. While security best practices are not within the scope of this article &ndash; at a minimum &ndash; add a firewall. _See_ [How to Setup a Firewall with UFW on an Ubuntu and Debian Cloud Server](https://www.digitalocean.com/community/articles/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server).

## Additional Resources

* 
* 
* 

As always, if you need help with the steps outlined in this How-to, look to the DigitalOcean Community for assistance by posing your question(s), below.

<p><div style="text-align: right; font-size:smaller;">Article submitted by: <a href="https://plus.google.com/107285164064863645881?rel=author" target="_blank">Pablo Carranza</a> &bull; DATE</div></p>

# ~~~ WIP ~ DRAFT ~ WIP ~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls)* gladly accepted