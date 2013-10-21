# ~~~~~~~~~~ DRAFT ~~~~~~~~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls) gladly accepted*

Setting Your Ubuntu Server's Hostname & Fully Qualified Domain Name (FQDN)
=

### Introduction

A hostname is a label or nickname that is assigned to a computer connected to a network and that is used to identify the machine in various forms of electronic communication such as the World Wide Web and/or email. Hostnames are important because they form part of a computer's Fully Qualified Domain Name (FQDN).

## Hostname Requirements

Internet standards for protocols mandate that component hostname labels may contain only the [ASCII](http://en.wikipedia.org/wiki/ASCII) letters `a` through `z` (in a case-insensitive manner), the digits `0` through `9`, and the hyphen (`-`). No other symbols, punctuation characters, or white space are permitted.

In addition to the above technical requirements, the only practical requirement of a server's hostname, for your environment(s), is that it should be something unique to the other servers within a particular domain.

### Restrictions on valid host names

Hostnames are composed of series of labels concatenated with dots, as are all domain names. For example, let's break `mail.google.com` into its components:

*	`mail` is the host; and
*	`google.com` is the domain.

Each label must be between 1 and 63 characters long, and the entire hostname (including the delimiting dots) has a maximum of 255 characters.

## Setting the Hostname

Enter following commands to set the hostname, replacing plato with the hostname of your choice:

	echo "plato" > /etc/hostname
	hostname -F /etc/hostname

If it exists, edit the file `/etc/default/dhcpcd` and comment out the `SET_HOSTNAME` directive (obviously, you can use whichever text editor you wish; but this guide assumes that you have installed the [vim text editor](https://www.digitalocean.com/community/articles/installing-and-using-the-vim-text-editor-on-a-cloud-server)); by executing:

	vim /etc/default/dhcpcd

(Then, tap on the `i` key and use the arrow keys on your keyboard to navigate the text area. Then,

	#SET_HOSTNAME='yes'

## Setting the Fully Qualified Domain Name (FQDN)

Execute

	vim /etc/hosts

Then, tap on the `i` key and modify your hosts file so that it resembles the following (**obviously,** substituting the [hostname], [yourdomain], [tld], and [YourIP] values):

	127.0.0.1	localhost.localdomain	localhost
	127.0.1.1	hostname.yourdomain.tld	hostname
	YourIP		hostname.yourdomain.tld	hostname

## Additional Resources

*	[Ubuntu Server 12.04 LTS Guide](https://help.ubuntu.com/12.04/serverguide/index.html)

# ~~~~~~~~~~ DRAFT ~~~~~~~~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls) gladly accepted*