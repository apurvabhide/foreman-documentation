Installing Foreman
==================

There are several different methods of installing Foreman. The recommended way is with the puppet based Foreman Installer but you may also use your distribution’s package manager or install directly from source.

System Requirements
-------------------


This sections outlines the system requirements for an installation of Foreman. This will cover the hardware requirements, OS requirements and firewall requirements. This includes variations for all supported database types.

Supported Platforms
^^^^^^^^^^^^^^^^^^^

The following operating systems are supported by the installer, have packages and are tested for deploying Foreman:

* Red Hat Enterprise Linux 7
   * Architectures: x86_64 only
   * EPEL is required
   * Enable the Optional repository/channel:
       * `yum-config-manager --enable rhel-7-server-optional-rpms`
       * check the above repositories because the command can silently fail when subscription does not provide it: 
       * `yum repolist`

   * Apply all SELinux-related errata.
* CentOS, Scientific Linux or Oracle Linux 7
   * Architectures: x86_64 only
   * EPEL is required
* Ubuntu 16.04 (Xenial)
   * Architectures: i386, amd64, aarch64
* Ubuntu 18.04 (Bionic)
   * Architectures: i386, amd64, aarch64
* Debian 9 (Stretch)
   * Architectures: i386, amd64, aarch64

It is recommended to apply all OS updates if possible.

All platforms will require Puppet 4 or higher, which may be installed from OS or the Puppet Labs repositories.

Other operating systems will need to use alternative installation methods, such as from source.

The following operating systems are known to install successfully from Foreman:

* RHEL and derivatives (CentOS, Scientific Linux, Oracle Linux) 3+
* Fedora
* Ubuntu
* Debian
* OpenSUSE
* SLES
* CoreOS
* Solaris
* FreeBSD
* Juniper Junos
* Cisco NX-OS

Hardware Requirements
^^^^^^^^^^^^^^^^^^^^^

The hardware requirements for Foreman depend primarily on the number of requests that it will receive, which depends on the number of configuration management clients, web UI activity and other systems using the API.

The default installation when including Puppet Server will require:

* 4GB memory
* 2GB disk space

For a bare minimum installation with few clients and no Puppet master, the requirements are:

* 2GB memory
* 1GB disk space

Scaling notes

* The default installation when using Passenger will increase the number of running processes based on the number of simultaneous requests, up to a default maximum of six instances. Each instance will typically require 0.5-1GB of memory, depending on the number of configured plugins.
* When using the Puppet master, consult the requirements outlined in the `Puppet System Requirements <https://docs.puppet.com/puppet/latest/reference/system_requirements.html#hardware>`_ and `Puppet Server System Requirements <https://docs.puppet.com/puppetserver/2.4/install_from_packages.html#system-requirements>`_.
* Disk usage will increase as more data is stored in the database (PostgreSQL by default), mostly for facts and reports. See the `reports cronjob configuration <https://www.theforeman.org/manuals/1.21/index.html#3.5.4PuppetReports>`_ to change how they are expired.

Puppet Compatibility
^^^^^^^^^^^^^^^^^^^^

Foreman integrates with Puppet and Facter in a few places, but generally using a recent, stable version will be fine. The exact versions of Puppet, Puppet Server and Facter that Foreman is compatible with are listed below. Foreman 1.18 is the last version that will support Puppet < 4.0. Report/fact processing and ENC with older versions may work in Foreman 1.19, but this workflow will not be tested.

* Puppet versions < 2.6.5 require Parametrized_Classes_in_ENC in Foreman to be disabled.

Puppet Server compatibility

  Puppet Server is the application for serving Puppet agents used by default in Puppet 4/5 AIO installations.

AIO installer compatibility

  The Foreman installer supports both AIO and non-AIO configurations when using Puppet 4/5, switching behavior automatically based on the version of Puppet installed (usually during the first run when answers are stored).

  When using an AIO installation of Puppet to run the installer, it will default to configuring the Puppet Server application, and when using a non-AIO version of Puppet, it defaults to a traditional Rack/Passenger configuration.

Facter compatibility

  Foreman is known to be compatible with all Facter 1.x to 3.x releases.

Puppet Enterprise compatibility

  The Foreman installer and packages are generally incompatible with Puppet Enterprise, however with some manual reconfiguration, individual Foreman components such as the smart proxy should work if needed (some further unsupported documentation can be found on the wiki). The installer in particular will conflict with a Puppet Enterprise installation. It is recommended that Foreman is installed using Puppet “open source”.

Browser Compatibility
^^^^^^^^^^^^^^^^^^^^^

Using the most recent version of a major browser is highly recommended, as Foreman and the frameworks it uses offer limited support for older versions.

The recommended requirements are as follows for major browsers:

* Google Chrome 54 or higher
* Microsoft Edge
* Microsoft Internet Explorer 10 or higher
*  Mozilla Firefox 49 or higher

Other browsers may work unpredictably.

Firewall Configuration
   Protect your Foreman environment by blocking all unnecessary and unused ports.

   Ports indicated with * are running by default on a Foreman all-in-one installation and should be open.

Foreman Installer
-----------------

The Foreman installer is a collection of Puppet modules that installs everything required for a full working Foreman setup. It uses native OS packaging (e.g. RPM and .deb packages) and adds necessary configuration for the complete installation.

Components include the Foreman web UI, Smart Proxy, Passenger (for the puppet master and Foreman itself), and optionally TFTP, DNS and DHCP servers. It is configurable and the Puppet modules can be read or run in “no-op” mode to see what changes it will make.

It’s strongly recommended to use the installer instead of only installing packages, as the installer uses OS packages and it saves a lot of time otherwise spent replicating configuration by hand.

By default it will configure:

* Apache HTTP with SSL (using a Puppet-signed certificate)
* Foreman running under mod_passenger
* Smart Proxy configured for Puppet, TFTP and SSL
* Puppet master running under mod_passenger
* Puppet agent configured
* TFTP server (under xinetd on Red Hat platforms)

Other modules can be enabled, which will also configure:

* ISC DHCP server
* BIND DNS server

It’s recommended to run the installer on a fresh and clean single-purpose system, since the configurations of the aforementioned components is (at least partially) overwritten by the installer.

Installation
^^^^^^^^^^^^

Downloading the installer

Follow the instructions in section `2.1 Quickstart installation <https://www.theforeman.org/manuals/1.21/index.html#2.1Installation>`_.

Running the installer

The installation run is non-interactive, but the configuration can be customized by supplying any of the options listed in `foreman-installer --help`, or by running `foreman-installer -i` for interactive mode. More examples are given in the Installation Options section. Adding -v will disable the progress bar and display all changes, while --noop will run without making any changes. To run the installer, execute:

  `foreman-installer`

After it completes, the installer will print some details about where to find Foreman and the Smart Proxy and Puppet master if they were installed along Foreman. Output should be similar to this:

    * Foreman is running at https://theforeman.example.com
          Initial credentials are admin / 3ekw5xtyXCoXxS29
    * Foreman Proxy is running at https://theforeman.example.com:8443
    * Puppetmaster is running at port 8140
    * The full log is at /var/log/foreman-installer/foreman-installer.log
