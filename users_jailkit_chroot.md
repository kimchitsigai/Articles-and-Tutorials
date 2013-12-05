~~~ WIP ~~~
===

*Pull Requests gladly accepted*

## How to Create Isolated File Directories For Ubuntu 12.04 Users with Jailkit

### Introduction

Of particular interest to hosting providers, resellers, and those that manage a server for friends &amp; family, the `chroot` (short for "change root directory") process provides a way of isolating users from other parts of your server &ndash; by putting them in (what is commonly referred to as) a jail. In other words, a `chroot` jail can be used to section off, or isolate, a particular user from entire sections of your server's filesystem. Without a `chroot` jail, nothing would prevent a user with even limited file permissions from being able to navigate up to system-critical, top-level directories.

### An Added Layer of Protection

Many control panels that reconfigure web servers for shared hosting will automatically create `chroot` directories for user accounts. Even if you trust your users to not intentionally attempt anything malicious, Brute-Force Attacks are an unwelcome reality in today's IT world and isolating your users' file directories provides an added layer of defense in the event that a user's system account should become compromised.

To create a `chroot` jail, simply create a folder that has a replication of the directory structure of a typical Linux server. The difference is that you *only* copy, in that `chroot` directory, the bare minimum of what is needed by your user. This process can be carried out manually, with several commands or you can automate the process with Jailkit. 

## About Jailkit

Jailkit is a set of utilities that can be used to setup a `chroot`-based, restricted environment where users have limited access to the server's filesystem and the commands they run. The Jailkit utilities also make it easy to setup a restricted shell or to run services or programs inside such a restricted environment.

>Project website:  
>[http://olivier.sessink.nl/jailkit/](http://olivier.sessink.nl/jailkit/)

## Prerequisite

* This article assumes that you have completed the steps outlined in [Initial Server Setup with Ubuntu 12.04 | DigitalOcean](https://www.digitalocean.com/community/articles/initial-server-setup-with-ubuntu-12-04).
	>#### SSH Keys
	>For increased security, it is advisable that you:  
	>1. Use SSH keys for system logins. *See* [How To Set Up SSH Keys | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-use-ssh-keys-with-digitalocean-droplets) (**Windows users:** Refer to the article cited, next); **_and_**  
	>2. Disable password logins. *See* [How To Create SSH Keys with PuTTY to Connect to a VPS | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-create-ssh-keys-with-putty-to-connect-to-a-vps).
* Jailkit needs to be compiled from source. To install the tools needed for that process, execute the following command in a terminal window:

		sudo apt-get -y install build-essential

## Install Jailkit

Download the latest version of the Jailkit source files and extract the archive in the <code>/tmp</code> directory, by executing the following command:

>**Note:** Check the Jailkit project website to ensure that you are installing the most recent release, i.e.
>
>		http://olivier.sessink.nl/jailkit/jailkit-<VERSION>.tar.gz

	cd /tmp && sudo wget -O - "http://olivier.sessink.nl/jailkit/jailkit-2.16.tar.gz" | tar xzvf -

Next, execute:

	cd jailkit-*

Finally, compile and install Jailkit, by executing:

	./configure && make && sudo make install

### Program Files & Utilities

By default, Jailkit installs its utilities into <code>/usr/sbin/</code> and its configuration and template files into <code>/etc/jailkit/</code>.

>**Note:** In some cases, the configuration files must be replicated into the `chroot` directory and edited appropriately. Additionally, Jailkit's utilities are prefixed with <code>jk_</code>. A utility that is run within the jail directory is able to read its configuration only from the jailed `chroot` directory.

All of Jailkit's utilities have man pages which contain more information about how to use them. You may also read more about them on Jailkit's website.

## Available Jailkit Utilities

Jailkit is comprised of various pre-configured templates & configuration files that you can mix-and-match, to build the perfect `chroot` jail. If none of the existing Jailkit utilities meets your needs, you can customize them or create new ones.

	user@server:/usr/sbin/$
	jk_addjailuser	jk_chrootlaunch	jk_cp		jk_jailuser	jk_lsh		jk_uchroot       
	jk_check		jk_chrootsh		jk_init		jk_list		jk_socketd	jk_update

## Create the Basic `chroot` Environment

## Conclusion

As you can see, creating and administering `chroot` jails is reduced to a fairly simple process with Jailkit. While `chroot` is very useful for basic, preventative security, it is not designed to prevent deliberate attempts to gain root access for purposes of attacking a server. For that threat, there are other security measures you can employ. Nevertheless, `chroot` helps, tremendously, to at least make it more difficult to exploit your virtual private server.

## Additional Resources

* Jailkit-users [mailing list](https://lists.nongnu.org/mailman/listinfo/jailkit-users)
* [How To Use Filezilla to Transfer & Manage Files Securely on your VPS | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-use-filezilla-to-transfer-and-manage-files-securely-on-your-vps)

As always, if you need help with the steps outlined in this How-To, look to the DigitalOcean Community for assistance by posing your question(s), below.

<p><div style="text-align: right; font-size:smaller;">Article submitted by: <a href="https://plus.google.com/107285164064863645881?rel=author" target="_blank">Pablo Carranza</a> &bull; DATE</div></p>