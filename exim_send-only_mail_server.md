# ~~~~~~~~~~ DRAFT ~~~~~~~~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls) gladly accepted*

Install a Send-only Mail Server for Your Apps on Ubuntu 12.04
====

### Introduction

Due to the popularity of Gmail, Google Apps, Outlook.com, Yahoo! Mail & a myriad of other providers, many cloud-server users mistakenly fail to install a mail server, initially. However, humans are not the only ones that send email. If fact, many Linux server applications also need to send email.

## Message Transfer Agent (MTA)

A Message Transfer Agent, or Mail Transfer Agent, transfers electronic mail messages from one computer to another. An MTA implements both the client (sending) and server (receiving) portions of the Simple Mail Transfer Protocol (SMTP).

Another popular MTA is [Postfix](https://www.digitalocean.com/community/articles/how-to-install-and-setup-postfix-on-ubuntu-12-04), but users that do not require a full-fledged mail server prefer the Exim send-only mail server because it is lightweight, compared to other MTAs. Thus, Exim is a good choice for WordPress installations.

## Prerequisites

This guide assumes that you have already:

* Set your droplet's hostname and Fully Qualified Domain Name (FQDN). *See* [Setting the Hostname & Fully Qualified Domain Name (FQDN) on Ubuntu 12.04](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/blob/master/set_hostname_fqdn_on_ubuntu.md);
* Created the necessary DNS records. *See* [How to Set Up a Host Name with DigitalOcean](https://www.digitalocean.com/community/articles/how-to-set-up-a-host-name-with-digitalocean); and
* Created an SPF record. *See* [How To use an SPF Record to Prevent Spoofing & Improve E-mail Reliability](https://www.digitalocean.com/community/articles/how-to-use-an-spf-record-to-prevent-spoofing-improve-e-mail-reliability).

## Update Current Software

First, you want to update the software packages already on your server; by executing:

	sudo apt-get update && sudo apt-get -y upgrade && sudo apt-get -y dist-upgrade && sudo apt-get -y autoremove

## Installation

Then, to install Exim and its dependencies, execute: 

	sudo apt-get -y install exim4

## Configure Exim for Local Mail Service

To configure Exim for your environment, execute:

	sudo dpkg-reconfigure exim4-config

and configure everything according to your needs. If you need to modify any of your settings, simply re-run the configuration wizard.

#### Mail Server Configuration Type

The first configuration window you encounter will ask you to select the "**mail server configuration type that best meets your needs**." If not already highlighted, use the arrow keys on your keyboard to select `internet site; mail is sent and received directly using SMTP`:

![Select the option for internet site](./images/exim4_internet_site.png)

Next, tap the <code>Tab</code> key (to highlight <code>&lt;Ok&gt;</code>) and press <code>Enter</code>.

#### Enter FQDN

The next configuration window you'll encounter will ask that you enter your system's fully qualified domain name (FQDN) in the `mail name` configuration screen:

![Enter your system's FQDN](./images/exim4_fqdn.png)

Next, tap the <code>Tab</code> key (to highlight <code>&lt;Ok&gt;</code>) and press <code>Enter</code>.

#### SMTP Listener

The ensuing configuration window will ask you to decide on which interfaces you would like `Exim` to "listen." Enter <code>127.0.0.1</code>, only:

![Tell Exim to listen on 127.0.0.1, only](./images/exim4_listen.png)

**Note:** DigitalOcean anticipates IPv6 support in the near future; at which time, you may want to instruct `Exim` to listen on both <code>127.0.0.1; ::1</code>.

Next, tap the <code>Tab</code> key (to highlight <code>&lt;Ok&gt;</code>) and press <code>Enter</code>.

#### Mail Destinations

The configuration prompt that follows will ask that you enter all of the destinations for which `Exim` should accept mail. List your:

* FQDN;
* local hostname;
* <code>localhost.localdomain</code>; and
* <code>localhost</code>:

![Enter mail destinations](./images/exim4_destinations.png)

Next, tap the <code>Tab</code> key (to highlight <code>&lt;Ok&gt;</code>) and press <code>Enter</code>.

#### Relay Options

Advanced configurations beyond the scope of this article allow you to use `Exim` as a relay mail server. In the next screen, leave the `relay mail` field blank:

![No need to configure relay domain(s) at this time](./images/exim4_relay.png)

Tap the <code>Tab</code> key (to highlight <code>&lt;Ok&gt;</code>) and press <code>Enter</code>.

The subsequent screen is a follow-up to the relay mail server option. Leave this window blank and tap the <code>Tab</code> key (to highlight <code>&lt;Ok&gt;</code>) and press <code>Enter</code>.

#### DNS Queries

Select **No** when asked whether to keep DNS queries to a minimum:

![Select No when asked whether to keep DNS queries to a minimum](./images/exim4_dns_queries.png)

Make sure that <code>&lt;No&gt;</code> is highlighted and press <code>Enter</code>.

#### Delivery Method

In the window that follows, choose whichever mail delivery method you'd like for incoming mail; although the <code>Maildir</code> format can make handling individual, locally-delivered mail messages easier:

![Choose the Maildir delivery method](./images/exim4_mail_format.png)

Next, tap the <code>Tab</code> key (to highlight <code>&lt;Ok&gt;</code>) and press <code>Enter</code>.

#### Configuration File

In the ensuing prompt, choose the default `unsplit` configuration file, by selecting <code>No</code>:

![Select unsplit config](./images/exim4_unsplit_config.png)

Make sure that <code>&lt;No&gt;</code> is highlighted and press <code>Enter</code>.

#### Postmaster address

In the last configuration window, enter at least one external email address (choose one that you check frequently) in addition to root when asked to specify postmaster mail recipients.

## Test Your Mail Configuration

At this juncture, let's send a test email, to make sure everything is configured correctly, by issuing the following command: (substituting `someone@somedomain.tld` for a valid, an external email address):

	echo "This is a test." | mail -s Testing someone@somedomain.told

(Check the recipient's SPAM folder, in the event that the SPF record is not configured correctly.)

## SMTP Authentication

While you now have a functioning send-only mail server, most users prefer taking advantage of the added security provided by `SMTP-AUTH` with `TLS` and `SASL`.

The first step is to create a self-signed certificate for use with `TLS`. Enter the following command into a terminal prompt:

	sudo /usr/share/doc/exim4-base/examples/exim-gencert

The system will then begin the process of creating a self-signed SSL certificate for Exim. This certificate is sufficient to establish encrypted connections, but, to to secure *identification*, you need to obtain a verified certificate from an third-party Certificate Authority (CA). *See* []().

## Additional Resources

* [Documentation for Exim](http://www.exim.org/docs.html)
* [Exim Wiki](http://wiki.exim.org/)

# ~~~~~~~~~~ DRAFT ~~~~~~~~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls) gladly accepted* 
