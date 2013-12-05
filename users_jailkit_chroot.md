~~~ WIP ~~~
===

*Pull Requests gladly accepted*

## How to Create Isolated File Directories For Ubuntu 12.04 Users with Jailkit

### Introduction

A `chroot` is a way of isolating applications and/or users  from other parts of your server, by putting them in (what is commonly referred to as) a jail. In other words, a `chroot` jail can be used to sectioned off a particular user from entire sections of your server's filesystem. Without a `chroot` jail, a user with even limited file permissions would still be able to navigate to top-level directories. Without `chroot`, nothing would prevent the user from navigating up to system-critical directories. Many control panels that reconfigure web servers for shared hosting will automatically create `chroot` directories for user accounts.

To create a jail, you simply create a folder that has a replication of the directory structure of a normal Linux box. The difference is that you only copy, in that `chroot` directory, the bare minimum of what you need. This process can be carried out manually or you can automate the process with Jailkit. 

## About Jailkit

Jailkit is a set of utilities to limit user accounts to specific files using `chroot` and or specific commands. Setting up a `chroot` shell, a shell limited to some specific command, or a daemon inside a `chroot` jail is a lot easier and can be automated using these utilities and can be used to secure cvs, sftp, shell or daemon processes.

>Project website:<br/>
>[http://olivier.sessink.nl/jailkit/](http://olivier.sessink.nl/jailkit/)

## Prerequisite

	sudo apt-get -y install build-essential

## Install Jailkit

Download the latest version of the Jailkit source files and extract the archive in the <code>/tmp</code> directory, by executing the following command in a terminal window:

>**Note:** Check the Jailkit project website to ensure that you are installing the most recent release, i.e.
>
>		http://olivier.sessink.nl/jailkit/jailkit-<VERSION>.tar.gz

	cd /tmp && sudo wget -O - "http://olivier.sessink.nl/jailkit/jailkit-2.16.tar.gz" | tar xzvf -

Next,

	cd jailkit-*

Finally, compile and install Jailkit, by executing:

	./configure && make && sudo make install

### Program Files

By default, Jailkit installs its binaries into <code>/usr/sbin/</code> and its configuration and template files into <code>/etc/jailkit/</code>. Note that in some cases, the configuration files must be replicated into the <code>[chroot]/etc/jailkit</code> directory and edited appropriately. A <code>jk</code> program that is run within the jail directory is able to read its configuration only from the jailed <code>[chroot]/etc/jailkit</code> directory.

## Available Jailkit Sets

Jailkit is comprised of various pre-configured templates & configuration files that you can mix-and-match, to build the perfect `chroot` jail. If none of the existing jail-sets meets your needs you can customize them or create new ones.

## Create the Basic `chroot` Environment

## Conclusion

As you can see setting the ssh `chroot` jail is a fairly simple process. If a user does not have its home user directory available in a `chroot` jail after login s/he will end up in /. You can create and further configure your `chroot` by creating a user home directory, defining bash environment, etc.

`Chroot` is very useful for basic preventative security, but it is not designed to prevent deliberate attempts to gain root access and attack a server. For that there are other security measures you can take. Nevertheless, `chroot` helps tremendously to at least make it more difficult to exploit your dedicated server.

## Additional Resources

* Jailkit-users [mailing list](https://lists.nongnu.org/mailman/listinfo/jailkit-users)
* [How To Use Filezilla to Transfer & Manage Files Securely on your VPS | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-use-filezilla-to-transfer-and-manage-files-securely-on-your-vps)
* [How To Create SSH Keys with PuTTY to Connect to a VPS | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-create-ssh-keys-with-putty-to-connect-to-a-vps) (which also outlines the steps required to establish password-less logins)

As always, if you need help with the steps outlined in this How-To, look to the DigitalOcean Community for assistance by posing your question(s), below.

<p><div style="text-align: right; font-size:smaller;">Article submitted by: <a href="https://plus.google.com/107285164064863645881?rel=author" target="_blank">Pablo Carranza</a> &bull; DATE</div></p>