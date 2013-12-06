~~~ WIP ~~~  
*Pull Requests gladly accepted*

How to Create Isolated File Directories For Ubuntu 12.04 Users with Jailkit
=

### Introduction

Of particular interest to hosting providers, resellers, and those that manage a server for friends &amp; family, the `chroot` (short for "change root directory") system provides a way of isolating users from other parts of your server &ndash; by putting them in (what is commonly referred to as) a jail. In other words, a `chroot` jail can be used to section off, or isolate, a particular user from entire sections of your server's filesystem. Without a `chroot` jail, nothing would prevent a user with even limited file permissions from being able to navigate up to system-critical, top-level directories.

## The Basic `chroot` Environment

A `chroot` jail is a directory tree that you create within your filesystem, where the user cannot see any directories or files that are outside the `chroot` jail directory. The user is said to be "jailed" in that directory and its subdirectories. If you want a user to be able to do just one task, you can set up a `chroot` jail so that the user is able to only do that one task.

>For example, if you want a user to be able to run SCP, install a copy of SCP in a `chroot` jail along with just enough support to execute the program (e.g., using a limited shell).

The fewer executables you have in a `chroot` jail (and the more their capabilities are limited, such as using strict configurations), the more work a hacker, or malicious worm, needs to break out of it.

Many control panels that reconfigure web servers for shared hosting will automatically create `chroot` directories for user accounts. Even if you trust your users to not intentionally attempt anything malicious, Brute-Force Attacks are an unwelcome reality in today's IT world and isolating your users' file directories provides an added layer of defense in the event that a user's system account should become compromised.

To create a `chroot` jail, simply create a folder that has a replication of the directory structure of a typical Linux server. The difference is that you *only* copy, in that `chroot` directory, the bare minimum of what is needed by your user. This process can be carried out manually, with several commands, or you can automate the process with Jailkit.

## About Jailkit

Jailkit is a set of utilities that can be used to setup a `chroot`-based, restricted environment where users have limited access to the server's filesystem and the commands they run. The Jailkit utilities also make it easy to setup a restricted shell or to run services or programs inside such a restricted environment.

>Project website:  
>[http://olivier.sessink.nl/jailkit/](http://olivier.sessink.nl/jailkit/)

## Prerequisites

* This article assumes that you have completed the steps outlined in [Initial Server Setup with Ubuntu 12.04 | DigitalOcean](https://www.digitalocean.com/community/articles/initial-server-setup-with-ubuntu-12-04).

* Jailkit needs to be compiled from source. To install the tools needed for that process, execute the following command in a terminal window:

		sudo apt-get -y install build-essential

## Compile &amp; Install Jailkit

Download the latest version of the Jailkit source files and extract the archive in your server's <code>/tmp</code> directory, by executing the following command:

>**Note:** Check the Jailkit project website to ensure that you are installing the most recent release, i.e.
>
>		http://olivier.sessink.nl/jailkit/jailkit-<VERSION>.tar.gz

	cd /tmp && sudo wget -O - "http://olivier.sessink.nl/jailkit/jailkit-2.16.tar.gz" | tar xzvf -

Next, execute:

	cd jailkit-*

Finally, compile and install Jailkit, by executing:

	./configure && make && sudo make install

## Jailkit Utilities

Jailkit is comprised of various pre-configured scripts, templates and configuration files that you can mix-and-match, to build the perfect `chroot` jail. If none of the existing Jailkit utilities meet your needs, you can customize them or create new ones. By default, Jailkit installs its utilities in <code>/usr/sbin/</code> and its configuration and template files in <code>/etc/jailkit/</code>.

**Note:** In some cases, the configuration files must be replicated in the `chroot` directory and edited appropriately.

In other words, a utility that is run within a `chroot` environment is able to read its configuration only from within the jailed `chroot` directory. Jailkit's utilities are prefixed with <code>jk_</code>.

	non-root_user@server:/usr/sbin/$
	jk_addjailuser	jk_check	jk_chrootlaunch	jk_chrootsh		jk_cp
	jk_init		jk_jailuser	jk_list		jk_lsh	jk_socketd	jk_update

These utilities include a launcher that can start a daemon in a jail; a `chroot` shell tool; a tool to limit binary execution; a tool to update and clean up a jail based on changes already made on a the system at large; and more. All of Jailkit's utilities have <code>man</code> pages which contain more information on how to use them; and can be accessed on your server by executing:

	man jailkit

You may also read more about its utilities on Jailkit's website.

## Setting up a `chroot` Jail Environment

There needs to be a directory where the entire jail environment will be setup. Jailed users will see this directory as the root directory of the server. You are free to choose whatever directory structure you wish, e.g. <code>/home/jail/</code>, <code>/var/chroot/</code>, <code>/jail</code>, etc.

#### The `jk_init` utility

Create a jail using the <code>jk\_init</code> utility and specify the jail's location and what jailed-programs you want included in the jail; and Jailkit will automatically create, and assign the appropriate permissions to, the root of the `chroot` jail if it does not already exist. By employing the <code>jk_init</code> utility, you can automate the jail-directory, and jailed-program, setup. To do so, execute (feel free to substitute <code>/chroot</code> with a directory of your choice):

	sudo jk_init -v -j /chroot basicshell editors extendedshell jk_lsh netutils ssh sftp

**Note:** While the above command makes the listed programs **available** inside the `chroot` jail, it is still necessary to authorize access to specific programs for individual jailed-users.

#### The `jk_lsh` utility

provides a special, non-interactive shell that limits the binaries it will execute to those that have been explicitly allowed. All other commands or regular (such as the bash) shell access are denied. That way, even if a user uploads their own binary file, the user will **not** be able to execute it.

This can be used to restrict an account to a specific use. For example, <code>jk\_lsh</code> can be used to make rsync-, cvs-, sftp- or scp-only accounts. Allowed actions are read from <code>/etc/jailkit/jk\_lsh.ini</code>. If you run <code>jk\_lsh</code> inside a `chroot` jail, make sure <code>jk_lsh.ini</code> is copied into that `chroot` jail, i.e. <code>/path/to/jail/etc/jailkit/jk\_lsh.ini</code>.

## Creating &amp; Jailing a User

First, execute (substituting <code>username</code> with one of your choosing):

	sudo adduser username

Follow the prompts to specify a password and provide the user's information requested by the system.

**Note:** This is a normal user that is created in the actual filesystem and  is **_not_** inside the `chroot` jail, yet.

#### Jail the user

If you want your jailed-user to have access to the bash shell (for example, to be able to SSH into your server), execute:

	sudo jk_jailuser -m -s /bin/bash -j /chroot username

Then, copy bash and its libraries into the `chroot` jail using the <code>jk_cp</code> utility:

	sudo jk_cp -v -f /chroot /bin/bash

Finally, edit the SSH configuration file:

	sudo vim /etc/ssh/sshd_config

>Then, tap on the <code>i</code> key (on your keyboard) to enter the Vim text editor's "insert mode."

and add the jailed user to the following directive:

	[...]
	AllowUsers your_username new_username

>To save your edit, and exit, tap on the following keys: <code>Esc</code>,<code>:</code>, <code>w</code>, <code>q</code>, <code>Enter</code>.

Reload SSH to incorporate the new setting:

	sudo reload ssh

>**Option 2:** To put the user inside the `chroot` jail with maximum restrictions (that is, with no interactive-shell access), execute:

>		sudo jk_jailuser -m -j /chroot username

#### Confirm the transfer of the system user account into the `chroot` jail

To confirm that the user was jailed, check the user's <code>/etc/passwd</code> file, by executing:

	cat /etc/passwd

Review the line that pertains to the newly-jailed user and inspect the last two elements to make sure that the user's:

1. home directory is now nested inside the `chroot` jail; and
2. shell is now a special utility named <code>jk_chrootsh</code>:

		username:x:[UserID]:[PrimaryGroupID]:[Full Name],,,:/path/to/jail/./home/username:/usr/sbin/jk_chrootsh

In addition to the modifications to the jailed-user's <code>/etc/passwd</code> file, the <code>jk_jailuser</code> utility also adds the user to a stripped-down <code>passwd</code> file located at <code>/path/to/jail/etc/passwd</code> and adds the user's group(s) to a stripped-down group file located at <code>/path/to/jail/etc/group</code>.

#### Provide privileges to the jailed-user

While, at this point, you have created a valid `chroot` jail, the jail is currently locked-down to the point of being unusable; because privileges have been stripped-down by the <code>jk_lsh</code> utility so much that your newly-jailed user is not allowed to login to a bash shell.

#### Allow bash in the `chroot` jail



**It is important to note that a `chroot` jail can be easily escaped if the user is able to elevate to the root level.** Thus, it is very important to prevent the user from doing so.

## Security Considerations

>A badly configured jail is a security risk! -The Jailkit Team.

If a jailed user or a jailed process can modify files in (for example) the `chroot`/lib/ or `chroot`/etc/ directory (i.e., those within the  `chroot` jail directory), the user can bypass security checks and gain root privileges.

#### No directory inside a `chroot` jail, except for a user's home or <code>/tmp</code>, directories should be writable by the user.

The root of the `chroot` jail, especially, should **not be writable** by the user. Jailkit utilities can be used to perform some basic checks to verify that a jail is secure and abort if a jail is not secure. Check your log files if things don not work as expected.

The server's super user (i.e., root), or any process running with root privileges, can always break out of a jail. It is, therefore, important that the processes inside a `chroot` jail do not have root privileges, nor have the means to receive those privileges. **Avoid setuid (+s) executables inside the jail.** If the jail is on a separate filesystem, the jail filesystem can mounted with the <code>nosuid</code> flag.

## Conclusion

As you can see, creating and administering `chroot` jails is reduced to a fairly simple process with Jailkit. While `chroot` is very useful for basic, preventative security, it is not designed to prevent deliberate attempts to gain root access for purposes of attacking a server. For that threat, there are other security measures you can employ. Nevertheless, `chroot` helps, tremendously, to at least make it more difficult to exploit your virtual private server.

## Additional Resources

* Jailkit-users [mailing list](https://lists.nongnu.org/mailman/listinfo/jailkit-users)
* [How To Use Filezilla to Transfer & Manage Files Securely on your VPS | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-use-filezilla-to-transfer-and-manage-files-securely-on-your-vps)

As always, if you need help with the steps outlined in this How-To, look to the DigitalOcean Community for assistance by posing your question(s), below.

<p><div style="text-align: right; font-size:smaller;">Article submitted by: <a href="https://plus.google.com/107285164064863645881?rel=author" target="_blank">Pablo Carranza</a> &bull; DATE</div></p>