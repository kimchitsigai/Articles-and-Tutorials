# ~~~~ WIP ~~~~ DRAFT ~~~~ WIP ~~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls) gladly accepted*

Share your Calendars, Address Books and Mail with SOGo Groupware Server on Ubuntu 12.04
====

### Introduction

SOGo is an open source, modern, scalable groupware server. It offers shared calendars, address books, and emails through your favorite Web browser and by using a native client such as Mozilla Thunderbird and Lightning.

## SOGo Features

* Scalable architecture suitable for deployments from dozens to many thousands of users
* Rich web-based interface that shares the look and feel, the features and the data of Mozilla 
Thunderbird and Lightning
* Improved integration with Mozilla Thunderbird and Lightning by using the SOGo Connector and the SOGo Integrator
* Two-way synchronization support with any SyncML-capable devices (BlackBerry, Palm, Windows CE, etc.) by using the Funambol SOGo Connector

SOGo is standard-compliant. It supports CalDAV, CardDAV, GroupDAV, iMIP and iTIP and reuses existing IMAP, SMTP and database servers &ndash; making the solution easy to deploy and interoperable with many applications. Mobile devices supporting the SyncML standard use the Funambol middleware to synchronize information.

## Requirements

SOGo reuses many components in an infrastructure. Thus, it requires the following:

* Database server (e.g. MySQL or PostgreSQL);
* LDAP server (e.g. OpenLDAP);
* SMTP server (e.g. Postfix);
* IMAP server (e.g. Cyrus or Dovecot).

This article will guide you through the installation of all of these programs on `Ubuntu 12.04`.

## Prerequisites

First, consult [How To Install and Configure a Basic LDAP Server on an Ubuntu 12.04 VPS | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-install-and-configure-a-basic-ldap-server-on-an-ubuntu-12-04-vps) and deploy an LDAP server.

#### Security Hardening

Any server accessible from the public Internet should be security hardened, and your groupware server is no exception:

* Change your SSH port from the default Port 22 to a random port **below 1024**, as described in **Step Five** of [Initial Server Setup with Ubuntu 12.04](https://www.digitalocean.com/community/articles/initial-server-setup-with-ubuntu-12-04);

* Configure a [firewall](https://www.digitalocean.com/community/articles/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server) and make sure to open your **custom SSH port** and **TCP Ports 25 &amp; 465**;
 * The default firewall configuration tool for Ubuntu is `ufw`. To open the necessary ports, simply execute:

    		sudo ufw allow [custom SSH port below 1024]/tcp
			sudo ufw allow 
			sudo ufw enable
			sudo ufw status verbose

* Either [disable password logins](https://www.digitalocean.com/community/articles/how-to-create-ssh-keys-with-putty-to-connect-to-a-vps) or deploy [Fail2ban](https://www.digitalocean.com/community/articles/how-to-protect-ssh-with-fail2ban-on-ubuntu-12-04) &amp; [DenyHosts](https://www.digitalocean.com/community/articles/how-to-install-denyhosts-on-ubuntu-12-04).

#### Hostname & FQDN

Set your server's hostname and Fully Qualified Domain Name by implementing the steps in [Setting the Hostname & Fully Qualified Domain Name (FQDN) on Ubuntu 12.04](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/blob/master/set_hostname_fqdn_on_ubuntu.md).

#### Login to `root`

You will be executing all of the commands that follow as the `root` user. You can switch from a local user to the `root` user by executing:

    sudo su

#### Timezone

You can change your server's timezone to whatever you want; altough it may be best to set it to the same timezone of most of your users.

    dpkg-reconfigure tzdata

#### Add SOGo Repository & GPG Public Key

Append the SOGo repository to your `apt source list`, by copying & pasting both lines, below, into the command line and pressing `Enter`:

    echo -e "deb http://inverse.ca/ubuntu-nightly precise precise\n\
	deb-src http://inverse.ca/ubuntu-nightly precise precise" > /etc/apt/sources.list.d/SOGo.list

Next, you must add SOGo's GPG public key to Ubuntu's `apt keyring`. To do so, execute the following commands:

	apt-key adv --keyserver keys.gnupg.net --recv-key 0x810273C4

Then, update your lists of available software packages, by executing:

	apt-get update

## SOGo Installation

Install SOGo by executing:

    apt-get -y install sogo

## Install PostgreSQL Database

Next, execute:

	apt-get -y install postgresql sope4.9-gdl1-postgresql

Next, create the SOGo database in PostgreSQL:

	su - postgres
	createuser --no-superuser --no-createdb --no-createrole --encrypted --pwprompt sogo
	createdb -O sogo sogo
	exit
	echo "host sogo sogo 127.0.0.1/32 md5" >> /etc/postgresql/9.1/main/pg_hba.conf

Finally, restart PostgreSQL:

	service postgresql restart

## Configure SOGo

Next, configure sogo by entering each of the following commands individually:

	su - sogo -s /bin/bash
	defaults write sogod SOGoTimeZone "America/Chicago"
	defaults write sogod OCSFolderInfoURL "postgresql://sogo:PostgreSQL_pwd@localhost:5432/sogo/sogo_folder_info"
	defaults write sogod SOGoProfileURL "postgresql://sogo:PostgreSQL_pwd@localhost:5432/sogo/sogo_user_profile"
	defaults write sogod OCSSessionsFolderURL "postgresql://sogo:PostgreSQL_pwd@localhost:5432/sogo/sogo_sessions_folder"
	defaults write sogod OCSEMailAlarmsFolderURL "postgresql://sogo:PostgreSQL_pwd@localhost:5432/sogo/sogo_alarm_folder"
	defaults write sogod SOGoUserSources '({CNFieldName = displayName;  IDFieldName = cn; UIDFieldName = sAMAccountName; IMAPHostFieldName =; baseDN = "cn=Users,dc=yourdomain,dc=tld"; bindDN = "cn=Administrator,cn=Users,dc=youdomain,dc=tld"; bindPassword = SambaPWD; canAuthenticate = YES; displayName = "Shared Addresses"; hostname = "localhost"; id = public; isAddressBook = YES; port = 389;})'
	defaults write sogod WONoDetach YES
	defaults write sogod WOLogFile -
	defaults write sogod WOPidFile /tmp/sogo.pid
	defaults write sogod SOGoDraftsFolderName "Drafts"
	defaults write sogod SOGoSentFolderName "Sent"
	defaults write sogod SOGoTrashFolderName "Trash"
	defaults write sogod SOGoIMAPServer "localhost:144"
	defaults write sogod SOGoSieveServer "sieve://127.0.0.1:4190"
	defaults write sogod SOGoSieveScriptsEnabled "YES"

#### Optional

If you want to allow users to add their own IMAP account in SOGo, add the following command:

	defaults write sogod SOGoMailAuxiliaryUserAccountsEnabled YES

Logout of the `sogo` user & return to the `root` user

	exit

Create a symbolic link to allow Samba4 to use the SOGo configuration file:

	ln -s ~sogo/GNUstep ~root/GNUstep

There is a small bug in the `init.d` of Sogo that holds up the start-up process. You must edit the `init` file:

	vim /etc/init.d/sogo

and add the `-b` argument at lines 70 and 88:

	#Line 70
	if ! start-stop-daemon -b -c $USER --quiet --start --pidfile $PIDFILE --exec $DAEMON -- $DAEMON_OPTS
	# Line 88
	start-stop-daemon -b -c $USER --quiet --start --pidfile $PIDFILE --exec $DAEMON -- $DAEMON_OPTS

#### Restart SOGo & Samba4:

	service samba4 restart && nohup /etc/init.d/sogo restart &

## Cyrus IMAP Installation

The installation of Cyrus-Imap is done with the following command:

	apt-get -y install cyrus-admin-2.4 cyrus-imapd-2.4 sasl2-bin

#### Configure saslauth Authentication

Cyrus needs to use Saslauth system in order to authenticate its users. All small setup of Sasl in order to use Samba4.

	vim /etc/default/saslauthd

and change the following lines:

	...
	START=yes
	...
	MECHANISMS="ldap"

Create the following file with the command

	vim /etc/saslauthd.conf

and paste the following content and by changing of course the Administrator password:

	ldap_servers: ldapi://%2Fvar%2Flib%2Fsamba%2Fprivate%2Fldapi
	ldap_search_base: dc=yourdomain,dc=tld
	ldap_filter: (cn=%u)
	ldap_version: 3
	ldap_auth_method: bind
	ldap_bind_dn: Administrator@yourdomain.tld
	ldap_bind_pw: pass1234
	ldap_scope: sub

Restart the service:

	service saslauthd restart

You can also check that you authentication works:

	testsaslauthd -u administrator -p pass1234

#### Cyrus Configuration

	vim /etc/cyrus.conf

Add the following line in SERVICES section:

	imapnoauth      cmd="imapd -U 30 -N" listen="127.0.0.1:144" prefork=0 maxchild=100

As always, if you need help with the steps outlined in this How-To, look to the DigitalOcean Community for assistance by posing your question(s), below.

<p><div style="text-align: right; font-size:smaller;">Article submitted by: <a href="https://plus.google.com/107285164064863645881?rel=author" target="_blank">Pablo Carranza</a> &bull; DATE, 2013</div></p>

# ~~~~ WIP ~~~~ DRAFT ~~~~ WIP ~~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls) gladly accepted*