How To Install & Configure Headless Dropbox as a Service on Ubuntu 12.04
====

### Introduction

Dropbox is a popular cloud, file-hosting service for sharing files and keeping files synchronized across various devices. Among other uses, Dropbox provides (i) a convenient way to back-up important files on your cloud server and/or (ii) the option of editing files on your virtual private server ("VPS"), locally, and having those modifications automatically synced on your VPS.

## Install Dropbox via command line

The Dropbox daemon is compatible with both the 32-bit and 64-bit Ubuntu server. To install, execute the following command in a terminal:

#### For 32-bit Operating System

	cd ~ && wget -O - "https://www.dropbox.com/download?plat=lnx.x86" | tar xzf -

#### For 64-bit Operating System

	cd ~ && wget -O - "https://www.dropbox.com/download?plat=lnx.x86_64" | tar xzf -

## Sync Files on Your VPS with Your Dropbox Account

Next, run the Dropbox daemon from the newly created <code>.dropbox-dist</code> folder, by executing:

	~/.dropbox-dist/dropboxd

You will be asked to copy and paste a link in a web browser to create a new account or add your VPS to an existing account. The instructions will look something like:

	This client is not linked to any account...
	Please visit https://www.dropbox.com/cli_link?host_id=36186kw8r2u75w4m6z47387vnp8y67xo to link this machine.

This process issues a token for Dropbox to use, so that you won't have to store your Dropbox password directly on your VPS. Now, copy &amp; paste the link to any web browser, on any computer &ndash; the effect of which will link your cloud server to your Dropbox account. If successful, the following message will be displayed:

	Client successfully linked, Welcome [Your_Name]!

To terminate the Dropbox linking process, hold down (on your keyboard) the <code>Ctrl</code> key and tap the <code>C</code> key.

Once you do, you will find that your Dropbox folder was automatically created in your home directory: <code>~/Dropbox</code>. Now, any files you drop into this folder will be synchronized with all of your other Dropbox clients, instantly.

>Your <code>~/Dropbox</code> folder will also soon fill up with, and sync, any files you save in your Dropbox folder on other client devices.

## Syncing Content Outside of the Dropbox Folder

The simplest way to sync files on your cloud server that reside outside of your <code>~/Dropbox</code> folder is to create a symbolic link.

#### Example

You can easily backup your web site's files, to your Dropbox account, by executing:

	sudo ln -s /var/www/example.com ~/Dropbox/example.com

This symbolic link will cause Dropbox to treat <code>/var/www/example.com</code> as if it resides inside the Dropbox folder in your home directory. All other Dropbox clients will see <code>example.com</code> as a folder within the Dropbox folder, and the files within the <code>example.com</code> directory will be available to you &ndash; across different devices on which you have installed other Dropbox clients.

**Practice Tip:** Not only will your files be backed up, but you can edit your files on other devices &ndash; such as editing WordPress theme or plugin files with [Notepad++](http://notepad-plus-plus.org/) on a local, Windows machine. Any modifications you make, locally, will automatically be synced on your web server &ndash; providing an efficient workflow.

## Make Dropbox Start up Automatically on Boot

Next, create an <code>init.d</code> script to start Dropbox as a system service and auto-start the daemon every time your server boots up. To do so, execute:

	sudo vim /etc/init.d/dropbox

>**Note:** The same script can be used for multiple users that wish to have their own Dropbox sync daemon running.

Then, tap (on your keyboard) the <code>i</code> key and copy &amp; paste the following script, replacing <code>user1</code> and, if applicable, <code>user2</code>:

	#!/bin/sh
	
	### BEGIN INIT INFO
	# Provides:			dropbox service
	# Required-Start:	$local_fs $network $remote_fs $syslog
	# Required-Stop:	$local_fs $network $remote_fs $syslog
	# Default-Start:	2 3 4 5
	# Default-Stop:		0 1 6
	# Short-Desc.:		Start dropbox service at boot time
	# Description:		Control dropbox service with the start-stop-daemon
	### END INIT INFO
	
	# users separated by spaces or no spaces if only 1 user
	DROPBOX_USERS="user1 user2"
	
	# location of daemon
	DAEMON=.dropbox-dist/dropbox
	
	start() {
	  echo "Starting dropbox..."
	  for dbuser in $DROPBOX_USERS; do
	    HOMEDIR=`getent passwd $dbuser | cut -d: -f6`
	    if [ -x $HOMEDIR/$DAEMON ]; then
	      HOME="$HOMEDIR" start-stop-daemon -b -o -c $dbuser -S -u $dbuser -x $HOMEDIR/$DAEMON
	    fi
	  done
	}
	
	stop() {
	  echo "Stopping dropbox..."
	  for dbuser in $DROPBOX_USERS; do
	    HOMEDIR=`getent passwd $dbuser | cut -d: -f6`
	    if [ -x $HOMEDIR/$DAEMON ]; then
	      start-stop-daemon -o -c $dbuser -K -u $dbuser -x $HOMEDIR/$DAEMON
	    fi
	  done
	}
	
	status() {
	  for dbuser in $DROPBOX_USERS; do
	    dbpid=`pgrep -u $dbuser dropbox`
	    if [ -z $dbpid ] ; then
	      echo "dropboxd for USER $dbuser: not running."
	    else
	      echo "dropboxd for USER $dbuser: running (pid $dbpid)"
	    fi
	  done
	}
	
	case "$1" in
	  start)
	    start
	    ;;
	
	  stop)
	    stop
	    ;;
	
	  restart|reload|force-reload)
	    stop
	    start
	    ;;
	
	  status)
	    status
	    ;;
	
	  *)
	    echo "Usage: /etc/init.d/dropbox {start|stop|reload|force-reload|restart|status}"
	    exit 1
	
	esac
	
	exit 0

To save the file, tap the following keys: <code>Esc</code>, <code>:</code>, <code>w</code>, <code>q</code>, <code>Enter</code>.

Next, you need to make the start-up script executable, by executing:

	sudo chmod a+x /etc/init.d/dropbox

Then, have the Dropbox daemon start automatically upon a server reboot by adding the script to the default system start-up run levels:

	sudo update-rc.d dropbox defaults

Now that Dropbox is a service, you can use Ubuntu’s standard commands to manipulate it. 

## Controlling the Dropbox Service

Finally, start the Dropbox daemon without the need to reboot your VPS:

	sudo service dropbox start

As previously mentioned, the <code>init.d</code> script you created will allow you to control the Dropbox client like any other Ubuntu service, e.g.

	sudo service dropbox start|stop|reload|force-reload|restart|status

## Dropbox CLI

It is recommended that you download the official [Dropbox CLI](http://www.dropboxwiki.com/tips-and-tricks/using-the-official-dropbox-command-line-interface-cli) to check the sync status of your files, by executing:

	wget -O ~/.dropbox/dropbox.py "https://www.dropbox.com/download?dl=packages/dropbox.py"

Make the Dropbox Python Script executable:

	sudo chmod +x ~/.dropbox/dropbox.py

Now, you can easily check the status of the Dropbox client (not to be confused with the <code>dropbox daemon</code>) by executing the following command:

	~/.dropbox/dropbox.py status

Get help with the capabilities of the <code>Dropbox CLI</code>, by executing the following command:

	~/.dropbox/dropbox.py help

You can also use the <code>exclude</code> command to keep specific files or folders from syncing.

	~/.dropbox/dropbox.py help exclude

## Revoke Account Token

To revoke the token that was issued for the Dropbox instance on your cloud server, open a web browser and navigate to [https://www.dropbox.com/account#security](https://www.dropbox.com/account#security). Then, click on <code>unlink</code> next to your cloud server's host name.

## Remove Dropbox Client

To remove Dropbox from your VPS, execute (each line, individually):

	sudo sudo service dropbox stop
	sudo rm /etc/init.d/dropbox
	sudo rm -rf ~/.dropbox* ~/Dropbox
	sudo update-rc.d dropbox remove

## Additional Resources

* [Dropbox Forums](https://forums.dropbox.com/)

As always, if you need help with the steps outlined in this How-To, look to the DigitalOcean Community for assistance by posing your question(s), below.

<p><div style="text-align: right; font-size:smaller;">Article submitted by: <a href="https://plus.google.com/107285164064863645881?rel=author" target="_blank">Pablo Carranza</a> &bull; DATE</div></p>
