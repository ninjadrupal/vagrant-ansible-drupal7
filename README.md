Webscope - Vagrant LAMP Ansible Drupal
======================================

A Drupal development platform in a box, with everything you would need to develop Drupal websites.

This includes the following technologies:

* MySQL
* Apache2
* PHP
* Git
* Drush (with the 'site audit' and 'hacked!', 'registry_rebuild' and 'terminus' modules)
* Xdebug
* Sendmail
* Adminer
* ImageMagick

Prerequisites
-------------

* Vagrant 1.6
* VirtualBox 4.2+
* Ansible
* NFS (This comes pre-installed on Mac OS X 10.5+ (Leopard and higher))

    sudo apt-get install nfs-kernel-server nfs-common portmap

To install Ansible use the following commands:

    sudo easy_install pip
    sudo pip install ansible

If easy_install is not installed run the following:

    sudo apt-get install python-setuptools

You may have to install some prerequisite python packages first:

    sudo pip install paramiko PyYAML jinja2 httplib2 markupsafe

You can also install the Vagrant Cachier plugin in order to cache apt-get and gem requests, which speeds up reprovisioning.

    vagrant plugin install vagrant-cachier

To support deprovisioning you also need to install the Vagrant Triggers plugin.

If you already have the needed elements then you can get started.

Usage
-----

When you first download that repo you will be unable to do anything with it as the system requires the use of a settings.yml file. In the 'provision' directory, copy & rename the default.settings.yml file to settings.yml - you can then tweak it to suit your needs.

Out of the box you will get the following Vagrant box options:

    webserver_hostname: 'drupal.local'
    webserver_hostname_alias: 'www.{{ webserver_hostname }}'

    # Vagrantfile configuration

    boxipaddress: "192.168.100.100"
    boxname: "webscope"

Tweak the above settings if you want to create a separate project.

IP addresses will need to be unique for each project.

For using xdebug, you will need to modify the following setting with your IP address on the local network:

    # Local IP address (used for xdebug)
    localhost_ip_address: 192.168.1.1

On Linux you can find out the IP by running

    ifconfig

With the settings.yml file in place you can get up and running using the following command:

    vagrant up

Setting up the box takes a few minutes but there is plenty out output to look at whilst Ansible runs through the provisioning steps. You can see the webroot of the Vagrant box by going to the address [www.drupal.local](http://www.drupal.local/). A local Ansible action will add an entry to your hosts file for the default IP address 192.168.100.100 so you don't need to alter it.

Note: You will be asked for your sudo password on two separate occasions. The first is used by Vagrant to setup a NFS share and the second is used by Ansible to alter your local hosts file so that you can easily access the box via a web browser.

To access the vagrant box use the following command:

    vagrant ssh

If you have changed any of the settings and want to re-provision the box then run the following command:

    vagrant provision

If you change any Vagrant settings (e.g. changing the memory in the box) you'll need to reload the box with the following command:

    vagrant reload

To temporarily shutdown the box use the following command:

    vagrant halt

To delete the box and the data it contains run the following command:

    vagrant destroy

When you run 'vagrant up' again you will get back the original box.

Additional
----------

The default IP address of the Vagrant box is 192.168.100.100.

Mailcatcher is installed as a default mail server for PHP and will therefore intercept all email sent through any website installed on the Vagrant guest. You can access MailCatcher via the following URL:
[http://www.drupal.local:1080/](http://www.drupal.local:1080/)

You can access Adminer via the following URL:
[http://adminer.drupal.local/](http://adminer.drupal.local/)

Adminer will automatically log you into the database when you open it. The local MySQL user details are as follows:
Username: user
Password: password

Xdebug has been configured to allow code profiling. You can activate this using the XDEBUG_PROFILE=true parameter ar the end of the URL. Like this: [http://www.drupal.local/?XDEBUG_PROFILE=true](http://www.drupal.local/?XDEBUG_PROFILE=true).
The profile output can be found in the directory /tmp/xdebug_profiles on the Vagrant guest.

Wiki
----

That setup is based on [https://bitbucket.org/philipnorton42/vlad/wiki](https://bitbucket.org/philipnorton42/vlad/wiki).