Why &amp; How-to Set Up a WordPress Local-Development Environment with Vagrant
====

### The Limitations of LAMP Stacks

Historically, when setting up a local development environment, WordPress developers resorted to traditional LAMP stacks (Linux + Apache + MySQL + PHP) such as MAMP, WAMP or XAMPP. Meanwhile, advances in WordPress development have landed most modern WordPress installations on virtual private servers ("VPS") running Nginx with configurations &ndash; that emphasize caching &ndash; tailored specifically toward speeding up WordPress. MAMP, WAMP or XAMPP, however, run on Apache; with no optimization for WordPress and with no widely-accepted configuration for caching or any other optimization measures.

Developers agree that it is "extremely important to make sure that your development environment is identical to the production environment, and matches staging and testing servers if you have those too."<sup>[<a title="Vagrant: What, Why, and How | Philip Sturgeon (this article provides a nice walk-through of installing VirtualBox and Vagrant; and using Vagrant to manage your virtual machine instances, while explaining how you can take advantage of Puppet (and provides code snippets) to provision various resources like PHP and PostgreSQL)." href="http://net.tutsplus.com/tutorials/php/vagrant-what-why-and-how/" target="_blank">#</a>]</sup> By not mirroring your environments, the chance increases that something that seems to work just fine in an Apache-based local development environment will fail to work on the production server. A common frustration with LAMP stacks today is that they, now, seldom match the environment of the live, or production, server. Cutting-edge server setups for WordPress hosting these days include some combination of Nginx, MySQL, PHP-FPM and Memcached (note the 'd'!) &ndash; at a minimum.

## Why Vagrant?

Even subtle differences between environments increase the likelihood that your code modifications, plugins or web apps will fail in the testing, or staging, environment &ndash; resulting in wasted time troubleshooting, when you otherwise could be developing further. Now, consider the possibility that you may want to test your WordPress configurations by throwing into the mix the PECL memcache (no 'd'!) extension, or Varnish, and you'll happily discover: *Why Vagrant*. With Vagrant, you can easily recreate the same environment, anywhere, (literally) no matter what operating system or platform you choose for development. You can provision machines locally &ndash; on top of VirtualBox or VMware &ndash; and in the cloud with various providers &ndash; such as Amazon Web Services (AWS),  DigitalOcean or RackSpace, among others.

### Not ready to break up with your beloved LAMP stack?

With Vagrant, even those that continue developing with a LAMP stack have discovered that "Vagrant ... seems to solve all of the issues I've had in the past when trying to get a local environment up for WordPress."<sup>[<a title="How To Run WordPress Locally With Vagrant | Blair Williams" href="http://blairwilliams.com/2012/04/12/run-wordpress-locally-with-vagrant/" target="_blank">#</a>]</sup> Lastly, Vagrant is portable &ndash; allowing other members of your team to create their development environments from the same configuration you use &ndash; ensuring that all your team members are running code through the same environment, against the same dependencies, all configured the same way; regardless of whether any of them are working from Linux, Mac OS X, or Windows workstations.

## Prerequisite for Windows users

Vagrant is configured with a shell, or an operating system's command line interface ("CLI"), and OpenSSH. Unfortunately, an SSH client is generally not distributed with Windows, by default. However, Windows users can connect to Vagrant with any of their favorite SSH clients, e.g. one of the shells provided in GitHub for Windows; a terminal in Cygwin; Git Bash or PuTTY, among others.

## <a id="start">Getting Started</a>

On your local workstation, you'll need to:

1.	Download & install the latest version of [VirtualBox](http://virtualbox.org/wiki/Downloads) for your operating system.

2.	Download the matching [VirtualBox Extension Pack](https://www.virtualbox.org/wiki/Downloads) (the same for all platforms) and install it on your computer;

	Having VirtualBox installed should make the Extension Pack a recognized file type.

3.	Download & install the latest version of [Vagrant](http://downloads.vagrantup.com/) for your operating system;

	The first step for any project is to configure the <a title="Vagrantfiles are called Vagrantfiles because the actual literal filename for the file is Vagrantfile (casing doesn't matter) &ndash; and are portable across every platform Vagrant supports." href="http://docs.vagrantup.com/v2/vagrantfile/" target="_blank">Vagrantfile</a>. The purpose of the **Vagrantfile** is twofold: (i.) Mark the root directory of your project (a lot of the configuration of Vagrant is relative to this root directory); and (ii.) Describe the kind of machine and resources you need to run your project, as well as what software to install and how you want to access it.

4.  **Mac OS X & Linux-variant users:** Open a shell, e.g. terminal, and create, or navigate to, the directory in which you'd like to save your Vagrantfile (on Ubuntu, this would look like):

		sudo mkdir ~/[name of your choosing]
		cd [newly-created folder]

	Vagrant has a built-in command for initializing the newly-created directory for usage with Vagrant: [vagrant init](http://docs.vagrantup.com/v2/cli/init.html). **NOTE:** You can also run **vagrant init** in a pre-existing directory to setup Vagrant for an existing project.

5.	Next, execute the initializing command:

		vagrant init

	This will place a **Vagrantfile** in your current directory. Vagrant is meant to run with one **Vagrantfile** per project, and the **Vagrantfile** is supposed to be committed to <a title="Comparison of revision control software | Wikipedia, the free encyclopedia" href="http://en.wikipedia.org/wiki/Comparison_of_revision_control_software" target="_blank">version control</a> (e.g. Git). This way, every person working with that project can benefit from Vagrant without any of the upfront legwork.

	One concept to be mindful of is Vagrant's use of [boxes](http://docs.vagrantup.com/v2/getting-started/boxes.html). Vagrant uses **.box** files as templates from which to spin up a new virtual machine. Instead of building a virtual machine from scratch &ndash; which would be a slow and tedious process &ndash; Vagrant uses a base image to quickly clone a virtual machine. These base images are referred to as *boxes*, and specifying the *box* to use for your Vagrant environment is always the next step after creating a new **Vagrantfile**.

6.	Next, execute the following commands:

	vagrant box add precise64 http://files.vagrantup.com/precise64.box
	vagrant up

	**Microsoft Windows users:** Regardless of the command line program, the next steps are the same. If you choose to continue with Windows' command prompt, you can open the relevant CLI by (i.) pressing, on your keyboard, the ```Windows``` key (ii.) followed by the <kbd>R</kbd> key; which will open the RUN dialog box. Now, (iii.) type:

		cmd

	and (iv.) press <kbd>Enter</kbd>.

	Next, execute the following commands (pressing <kbd>Enter</kbd> after each line):

		cd C:\HashiCorp\Vagrant\bin
		vagrant box add precise64 http://files.vagrantup.com/precise64.box
		vagrant init precise64
		vagrant up

	**NOTE:** Sometimes, Windows workstations will spit out an error message when attempting to execute an initial Vagrant command. Restarting the computer usually remedies most post-installation issues. Later, if you get an error message to the effect that an "ssh executable is not found," and restarting the computer did not update the path, automatically, you can set the SSH PATH by executing the following command (assuming the ssh.exe file is in the folder referenced, below):

		set PATH=%PATH%;C:\Program Files (x86)\Git\bin</ol>

After running the above commands, you'll have a fully running virtual machine in VirtualBox running Ubuntu 12.04 LTS 64-bit. You can connect to this machine, via SSH, with the command <a href="http://docs.vagrantup.com/v2/cli/ssh.html" target="_blank">vagrant ssh</a>

>#### Using PuTTY?

>PuTTY is not compatible with OpenSSH, out-of-the-box. Consequently, PuTTY will not recognize the **insecure_private_key** provided by Vagrant as a valid private key. A workaround is to use PuTTYgen to **Load** (i.e. import) the **insecure_private_key** found in the **.vagrant.d** folder in your Home Directory, e.g.<ul><li>Windows XP: C:\Documents and Settings\{your username}\.vagrant.d\</li><li>Windows 7: C:\Users\{your username}\.vagrant.d\</li></ul>and convert the key file into PuTTY's format (a **.ppk** file) by clicking on the **Save private key** button. Then, launch PuTTY and enter the following connection information:

<table align="center" border="1">
<tr><th>Category</th><th>Sub-category</th><th>Field</th><th>Value</th></tr><tr><td>Session</td><td></td><td><b>Host Name:</b></td><td>127.0.0.1</td></tr><tr><td></td><td></td><td><b>Port:</b></td><td>2222</td></tr><tr><td></td><td></td><td><b>Connection type:</b></td><td>SSH</td></tr><tr><td>Connection</td><td>Data</td><td><b>Auto-login username:</b></td><td>vagrant</td></tr><tr><td>Connection/SSH</td><td>Auth</td><td><b>Private key file for authentication:</b></td><td>Click on the <b>Browse</b> button and find the <b>.ppk</b> private key you just converted</td></tr><tr><td>Session</td><td></td><td><b>Saved Sessions</b></td><td>vagrant (and then click the <b>Save</b> button for the <b>Load, save or delete a stored session</b> area)</td></tr></table>

>Finally, click on the **Open** button, at the bottom of the PuTTY window, to log in automatically to your Ubuntu virtual machine;

and when you're done playing around, you can remove all traces of it with the command <a href="http://docs.vagrantup.com/v2/cli/destroy.html" target="_blank">vagrant destroy</a>. The <a href="http://docs.vagrantup.com/v2/getting-started/boxes.html" target="_blank">Using a Box</a> and subsequent sections of <a href="http://docs.vagrantup.com/v2/" target="_blank">Getting Started | VagrantDocs</a> are quite good &ndash; please refer to that guide for a walk-through of setting up a more complete project, if you would like to continue building on your own, custom Vagrant box. Once you've got your box all set up, you can deploy WordPress, and whatever other software packages you'd like. However, if you're a little timid about 'flying solo' ...

### Not to Fear, the Power of the Open-source Community is Here!

If you'd like to get a jump-start on developing WordPress, locally, with Vagrant, check out *Varying Vagrant Vagrants for WordPress Development*, on GitHub (an exploration into the world of Vagrant and how it can help make development efficient and in sync with production systems; by replacing the common MAMP or XAMPP setups that we have become familiar with, while ensuring that all members of the team can develop in the same environment for a project without worrying about the operating system on their local machine).

With *Varying Vagrant Vagrants*, you will be able to fire up an instance by executing the simple **vagrant up** command, that will automatically install Nginx, PHP-FPM, and MySQL; before proceeding to move configuration files around and import SQL dumps &ndash; so that just minutes after the initial command, you can go to an existing development site in your browser or initiate a brand new WordPress install.<sup>[<a title="Evolving WordPress Development With Vagrant | Jeremy Felt" href="http://jeremyfelt.com/code/2013/03/17/evolving-wordpress-development-with-vagrant/" target="_blank">#</a>]</sup> And so, without further ado:

1.  At a minimum, complete Steps 1 - 3 under <a href="#start">Getting Started</a>, above;

2.  **OS X & Linux-variant users:** Open a terminal and create, or navigate to, the directory in which you'd like to save your work (on Ubuntu, this would look like):

		sudo mkdir -p /srv/[name of your choosing]
		cd /srv/[newly-created folder]

	and clone *Varying Vagrant Vagrants* from GitHub, by executing the following commands:

		sudo git clone https://github.com/10up/varying-vagrant-vagrants [local folder name of your choosing]

	**Windows users:** that downloaded & installed [GitHub for Windows](http://windows.github.com/) can click on the
![GitHub for Windows Button](http://windows.github.com/images/mini-clone-in-windows.png)
	button (the default storage directory can be changed under *tools* => *options* &ndash; in addition to the default shell); or, if you would rather not use GitHub for Windows, you can [download](https://github.com/10up/varying-vagrant-vagrants/archive/master.zip) a ZIP file of the repository to a local directory, that you'd like to use for your **Vagrantfile**, and extract it from there.

3.  Now, in a terminal or Windows command prompt, navigate to the new directory:

		cd [new local folder w/repository from Step 2, above]

	and execute the following command:

		vagrant up

	With that, your virtual machine is running (the first time you **vagrant up**, however, Vagrant can take approx. 30-60 minutes, as it downloads all the pre-packaged software and relevant updates). Test it immediately by going to **192.168.50.4** in a web browser. To start working with WordPress, one more step is necessary.

4.  Modify your local machine's hosts file so that **local.wordpress.dev** is mapped to **192.168.50.4**;<sup>[<a title="Windows users can find instructions on altering the hosts file on the Microsoft website." href="http://support.microsoft.com/kb/923947" target="_blank">#</a>]</sup>

	Once mapped, visiting **local.wordpress.dev** in your browser will bring up an initial WordPress installation. Follow through that to create your first WordPress development environment in Vagrant. Themes and plugins that you are developing can go into the relative **www/wordpress-default/wp-content/** directories.

From here, you should experiment. If you are familiar with object caching, the common plugins for working with PECL memcache can be installed. Memcached itself is already installed and running for when you're ready. If you start poking around the internal [documentation](https://github.com/10up/varying-vagrant-vagrants/blob/master/README.md) for *Varying Vagrant Vagrants*, you'll see quite a few places where you can hook in with customizations of your own to continue to extend the development environment. For alternative approaches, that have also done a lot of the upfront legwork for you, check out: [Getting Started With Vagrant | Scott Warren](http://www.thisprogrammingthing.com/2013/getting-started-with-vagrant/) or [Getting Started with Vagrant for WordPress Development | Mike Green](http://fifthroomcreative.com/archives/blog/2013/02/getting-started-with-vagrant/#.UfRXfdId5VI).

## Additional Resources

* [Development Environments Made Easy with Vagrant and DigitalOcean](https://www.digitalocean.com/blog_posts/development-environments-made-easy-with-vagrant-and-digitalocean);
* [How to Install Vagrant on a VPS Running Ubuntu 12.04](https://www.digitalocean.com/community/articles/how-to-install-vagrant-on-a-vps-running-ubuntu-12-04);
* [How to Use Vagrant on Your Own VPS Running Ubuntu](https://www.digitalocean.com/community/articles/how-to-use-vagrant-on-your-own-vps-running-ubuntu);
* [How to Use DigitalOcean as Your Provider in Vagrant on an Ubuntu 12.10](https://www.digitalocean.com/community/articles/how-to-use-digitalocean-as-your-provider-in-vagrant-on-an-ubuntu-12-10);
* [How To Install VirtualBox on Ubuntu 12.10 x64 | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-install-virtualbox-on-ubuntu-12-10-x64);
* [How to Install Git on Ubuntu 12.04 | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-install-git-on-ubuntu-12-04);
* [How to Use Git Effectively | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-use-git-effectively);
* [How to Use Git Branches | DigitalOcean](https://www.digitalocean.com/community/articles/how-to-use-git-branches);
* [Git Reference | by the GitHub team](http://gitref.org/).


As always, if you need help with the steps outlined in this HowTo, look to the DigitalOcean Community for assistance by posting your question(s), below.

<p><div style="text-align: right; font-size:smaller;">Article Submitted by: <a href="https://plus.google.com/107285164064863645881?rel=author" target="_blank">Pablo Carranza</a> &bull; updated 11/20/2013</div></p>
