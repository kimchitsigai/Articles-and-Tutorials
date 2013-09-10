HowTo Deploy The Foreman & Puppet on a Cloud Server
===================================================

The Foreman is a complete lifecycle management tool for physical and virtual servers. Through deep integration with configuration management, DHCP, DNS, TFTP, and PXE-based unattended installations, Foreman manages every stage of the lifecycle of your physical or virtual servers. The Foreman provides comprehensive, auditable interaction facilities including a web frontend and robust, RESTful API.

#### Provisioning

Provision on bare-metal & public or private clouds all from one place with one simple process.

#### Configuration

A complete configuration management solution, including an External Node Classifier (ENC), for Puppet, with built-in support for parameterized classes and hierarchical parameter storage.

#### Monitoring

Collect Puppet reports and facts. Monitor host configuration. Report status, distribution and trends.

### Foreman Architecture

A Foreman installation will always contain a central foreman instance that is responsible for providing the Web based GUI, node configurations, initial host configuration files, etc. However, if the foreman installation supports unattended installations then other operations need to be performed to fully automate this process. The smart proxy manages remote services and is generally installed with all Foreman installations to allow for TFTP, DHCP, DNS, and Puppet, and the Puppet CA.

## Gettings Started

The Foreman installer is a collection of Puppet modules that installs everything required for a full working Foreman setup. It uses native OS packaging (e.g. RPM and .deb packages) and adds necessary configuration for the complete installation.

Components include the Foreman web UI, Smart Proxy, Passenger (for the puppet master and Foreman itself), and optionally TFTP, DNS and DHCP servers. It is configurable and the Puppet modules can be read or run in "no-op" mode to see what changes it will make.

#### Supported platforms

* RHEL derivatives (CentOS), version 6 (EPEL required, plus subscribe to "optional" channel on RHEL 6)
* Debian 7 (Wheezy)
* Ubuntu 12.04 (Precise)

Other operating systems will need to use alternative installation methods see the [manual](http://theforeman.org/manuals/1.2/index.html).

### Installation

Install Puppet (without a master).

##### Download the Foreman installer

For Red Hat variants
''' yum -y install http://yum.theforeman.org/releases/1.2/el6/x86_64/foreman-release.rpm
yum -y install foreman-installer '''