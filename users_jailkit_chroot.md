~~~ WIP ~~~
===

*Pull Requests gladly accepted*

## How to Create Isolated File Directories For Ubuntu 12.04 Users with Jailkit

### Introduction

A chroot is a way of isolating applications and/or users  from other parts of your server, by putting them in (what is commonly referred to as) a jail. In other words, a chroot jail can be used to sectioned off a particular user from entire sections of your server's filesystem. Without a chroot jail, a user with even limited file permissions would still be able to navigate to top-level directories. Without chroot, nothing would prevent the user from navigating up to system-critical directories. Many control panels that reconfigure web servers for shared hosting will automatically create chroot directories for user accounts.

## Create Basic chroot Environment

To create a jail, you simply create a folder that has a replication of the directory structure of a normal Linux box. The difference is that you only copy, in that chroot directory, the bare minimum of what you need. 

	sudo mkdir /var/chroot

Next, copy the bash binary and its all shared library dependencies.

	ldd /bin/bash
        linux-vdso.so.1 =>  (0x00007fff9a373000)
        libtinfo.so.5 => /lib/x86_64-linux-gnu/libtinfo.so.5 (0x00007f24d57af000)
        libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f24d55ab000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f24d51eb000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f24d59f8000)

Now, we need to manually create all necessary directories and copy <code>/bin/bash</code> and all libraries to the new chroot directory into an appropriate location:

	sudo cd /var/chroot/
	sudo mkdir bin/ lib64/ lib/
	sudo cp /lib/x86_64-linux-gnu/libtinfo.so.5 lib/
	sudo cp /lib/x86_64-linux-gnu/libdl.so.2 lib/
	sudo cp /lib/x86_64-linux-gnu/libc.so.6 lib/
	sudo cp /lib64/ld-linux-x86-64.so.2 lib64/
	sudo cp /bin/bash bin/

At this point all is ready and we can chroot:

	sudo chroot /vat/chroot

Next, create a script:

	sudo vim ~/chroot-setup.sh

Then, tap on the <code>i</code> key (on your keyboard) and copy & paste the following content:

	#!/bin/bash
	# This script can be used to create a simple chroot environment
	# Written by LinuxCareer.com <http://linuxcareer.com/>
	# (c) 2013 LinuxCareer under GNU GPL v3.0+
	
	CHROOT='/var/chroot'
	mkdir $CHROOT
	
	for i in $( ldd $* | grep -v dynamic | cut -d " " -f 3 | sed 's/://' | sort | uniq )
	  do
	    cp --parents $i $CHROOT
	  done
	
	# ARCH amd64
	if [ -f /lib64/ld-linux-x86-64.so.2 ]; then
	   cp --parents /lib64/ld-linux-x86-64.so.2 /$CHROOT
	fi
	
	# ARCH i386
	if [ -f  /lib/ld-linux.so.2 ]; then
	   cp --parents /lib/ld-linux.so.2 /$CHROOT
	fi
	
	echo "Chroot jail is ready. To access it execute: chroot $CHROOT"

>**Note:** By default, the above script will create chroot in <code>/var/chroot</code> as defined by the <code>$CHROOT</code> variable. Feel free to change this variable according to your needs.

Now, make the script executable:

	sudo chmod +x ~/chroot-setup.sh

and run it with the file full path to your executables and files you wish to include. For example, if you need: ls, cat, echo, rm, bash, vi then use the which command to get a full path and supply it as an argument to the above chroot.sh script:

	sudo ./chroot-setup.sh /bin/{ls,cat,echo,rm,bash} /usr/bin/vi /etc/hosts
	Chroot jail is ready. To access it execute: chroot /var/chroot

Now, you can access your new chroot jail with:

	chroot /var/chroot
	bash-4.2# echo linuxcareer.com > file
	bash-4.2# cat file
	linuxcareer.com
	bash-4.2# rm file
	bash-4.2# vi --version
	VIM - Vi IMproved 7.3 (2010 Aug 15, compiled May  4 2012 04:25:35)

## Create chroot User Group

A this point, we need to create a separate usergourp, which will be used by sshd to redirect all users belonging to this usergroup to the chroot jail.

	sudo groupadd chrootjail

Now, add existing users to this group, if any:

	sudo adduser tester chrootjail
	Adding user `tester' to group `chrootjail' ...
	Adding user tester to group chrootjail
	Done.

## Configure sshd for chroot Jail

All what remains is to configure sshd to automaticaly redirect all users from the chrootjail usergroup to the chroot jail at /var/chroot. This can be easily done be editing the sshd configuration file /etc/ssh/sshd_config. Add the following to /etc/ssh/sshd_config:

	Match group chrootjail
            ChrootDirectory /var/chroot/

Restart ssh:

	sudo service ssh restart

## Login to chroot Jail via SSH

At this point you can test your settings by log in to you server with configured sshd:

	ssh tester@Your_droplet's_IP

## Conclusion

As you can see setting the ssh chroot jail is a fairly simple process. If a user does not have its home user directory available in a chroot jail after login s/he will end up in /. You can create and further configure your chroot by creating a user home directory, defining bash environment, etc.

Chroot is very useful for basic preventative security, but it is not designed to prevent deliberate attempts to gain root access and attack a server. For that there are other security measures you can take. Nevertheless, chroot helps tremendously to at least make it more difficult to exploit your dedicated server.

## Additional Resources

* [BasicChroot | Ubuntu Docs](https://help.ubuntu.com/community/BasicChroot)
* [How To Use Filezilla to Transfer & Manage Files Securely on your VPS | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-use-filezilla-to-transfer-and-manage-files-securely-on-your-vps)

As always, if you need help with the steps outlined in this How-To, look to the DigitalOcean Community for assistance by posing your question(s), below.

<p><div style="text-align: right; font-size:smaller;">Article submitted by: <a href="https://plus.google.com/107285164064863645881?rel=author" target="_blank">Pablo Carranza</a> &bull; DATE</div></p>
