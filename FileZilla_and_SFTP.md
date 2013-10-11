FileZilla & SFTP for Those Intimidated by the Command Line
===

### Introduction

With DigitalOcean's entrance into the unmanaged cloud-server market, hoards of novice WordPress or website developers  have migrated to the wonderful world of Virtual Private Servers ("VPS"). If you are a recent shared-hosting convert and/or your DigitalOcean droplet is your first VPS, you may be wondering what you got yourself into. Not to fear, however; this article is here to spare GUI Ninjas some headaches (if you do not know what a *GUI Ninja* is, you probably are one &ndash; don't worry, though, it is nothing to be ashamed of).

## FTP vs. SCP vs. SFTP

When needing to transfer files to-and-from your VPS in real time, you *technically* have three options. However, you should never, ever, **ever** connect to a remote server via FTP; given that both SCP and SFTP are just as easy to use, but provide much more security. In regard to SCP vs. SFTP, the two protocols are fairly equal &ndash; with SFTP having a slight edge in that an interrupted file-transfer can resume where it left off in the event of a broken connection.

## SFTP Clients

There are several quality SFTP clients out there: [Cyberduck](http://en.wikipedia.org/wiki/Cyberduck), [Filezilla](http://en.wikipedia.org/wiki/Filezilla) or [WinSCP](http://winscp.net/), to name a few. This article, however, will focus on Filezilla &ndash; an open-source (i.e., free) FTP client for Windows, Mac OS X and GNU/Linux. In addition to being able to download the program, the [filezilla-project.org/](https://filezilla-project.org/) site also contains a documentation [Wiki](https://wiki.filezilla-project.org/Main_Page) and a [Forum](https://forum.filezilla-project.org/).

## Key-based Authentication

With SFTP, you have two options when connecting to a remote server: (i.) passwords or (ii.) SSH keys. For a discussion on the benefits of SSH keys over passwords and/or instructions on setting up password-less logins, please refer to [How To Create SSH Keys with PuTTY to Connect to a VPS](https://www.digitalocean.com/community/articles/how-to-create-ssh-keys-with-putty-to-connect-to-a-vps).

## SFTP via SSH2 Key-based Authentication

FileZilla has a built-in key-management page in the Settings dialog, which allows you to save your Public Key &ndash; to (securely) automate the process of connecting to a remote server. If you have yet to create a Public Key, you can do so by following one of two [DigitalOcean tutorials](https://www.digitalocean.com/community/articles):

* Windows users: [How To Create SSH Keys with PuTTY to Connect to a VPS](https://www.digitalocean.com/community/articles/how-to-create-ssh-keys-with-putty-to-connect-to-a-vps);
* Mac OSX & Linux users: [How To Set Up SSH Keys](https://www.digitalocean.com/community/articles/how-to-set-up-ssh-keys--2).

Once you have an SSH key pair that you would like to use to connect to your VPS,

1.  open the FileZilla client.
2.  Then, click on ```Edit``` and select ```Settings```.
3.  On the left side of the menu, expand the ```Connection``` section and highlight ```SFTP```.
4.  Finally, click on the ```[Add key file...]``` button and browse your local machine's directories and select your Public Key file.
5.	Then, from FileZilla's home screen, click on ```File``` and select ```Site Manager```.
6.	Next, on the left side of the Site Manager, click on the ```New Site``` button and type a unique name, under ```My Sites``` that will allow you to easily identify this particular remote server in the future.
7.	Now, under the ```General``` tab, fill in the ```Host``` (with either an IP address or [FQDN](https://www.digitalocean.com/community/articles/how-to-set-up-a-host-name-with-digitalocean)) and ```Port``` fields (default is 22).
8.	In the ```Protocol``` dropdown menu, select ```SFTP - SSH File Transfer Protocol```.
9.	In the ```Logon Type``` dropdown menu, select ```Interactive```.

**Note for PuTTY users with passphrase-protected public keys:** If your original ```.ppk``` file is password-protected, FileZilla will convert your ```.ppk``` file to an unprotected one, when importing the key into FileZilla. As of version 3.0.10, a password-protected key file is not yet supported.

If a password-protected key file is desired, FileZilla is able to utilize [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)'s [Pageant](http://the.earth.li/~sgtatham/putty/0.63/htmldoc/Chapter9.html#pageant) tool.

1.  Simply run Pageant and, in your system tray, you will see the Pageant icon appear.
2.  Right-click on the icon and select ```Add Key``` and select your private key (.ppk) file.
3.  Then, follow the prompt to enter your passphrase.
4.  Finally, launch FileZilla and connect to your server via SFTP using SSH2 with a username and an **empty password** (*do not forget to close pageant when you are done*).

## Editing Text Files

FileZilla does not carry a built-in text editor; which gives you the freedom of using any text editor of your choice. A popular editor among Windows users is [Notepad++](http://notepad-plus-plus.org/) because it is lightweight and can work with many of today's popular [programming languages](http://en.wikipedia.org/wiki/Notepad%2B%2B#Programming_languages).

By default, FileZilla is configured to utilize your system's default editor. If you do not wish to make Notepad++ your system's default text editor, but would nevertheless like to use it to edit HTML, XML, Python, CSS, PHP & other programming files on your remote server:

1.	Click on ```Edit``` and select ```Settings```.
2.	Along the left side of the Settings window, highlight ```File editing```.
3.	Then, select the radio button associated with ```Use custom editor``` and click on the ```Browse``` button.
4.	Find your desired editor's executable (```.exe``` on Windows machines), double-click on it, and click the ```OK``` button to save your changes & close the Settings window.

## Additional Resources

* [How To Install Wordpress, Nginx, PHP, and Varnish on Ubuntu 12.04](https://www.digitalocean.com/community/articles/how-to-install-wordpress-nginx-php-and-varnish-on-ubuntu-12-04)

As always, if you need help with the steps outlined in this HowTo, look to the DigitalOcean Community for assistance by posing your question(s), below.

Article Submitted by: [Pablo Carranza](https://plus.google.com/107285164064863645881?rel=author) &bull; October 11, 2013
