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

You can use the same IP address for each box, but in that case you won't be able to launch and access both at the same time.
That IP address is written to your local hosts file on every `vagrant up` and is removed from the hosts file on `vagrant halt`

For using xdebug, you will need to modify the following setting with your IP address on the local network:

    # Local IP address (used for xdebug)
    localhost_ip_address: 192.168.1.1

You can find out the IP by running `ifconfig`. On OS X localhost IP address can be found in network settings.

To add a nice touch to you box, you can modify `provision/playbooks/roles/base/templates/motd.j2`.
Use [http://patorjk.com/software/taag/#p=display&f=Standard&t=Webscope%20-%20Drupal](http://patorjk.com/software/taag/#p=display&f=Standard&t=Webscope%20-%20Drupal) to create your message of the day.
That needs to be done before the initial build.

With the settings.yml file in place you can get up and running using the following command:

    vagrant up

Setting up the box takes about 15 minutes the first time, because Ansible provisioning script will download and install all the needed packages.
You can see the webroot of the Vagrant box by going to the address [www.drupal.local](http://www.drupal.local/).
A local Ansible action will add an entry to your hosts file for the box IP address (192.168.100.100 by default) so you don't need to alter it.

Next time you run `vagrant up` the process will take less than a minute.

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
You can access Adminer via the following URL:
[http://adminer.drupal.local/](http://adminer.drupal.local/)

Adminer will automatically log you into the database when you open it. The local MySQL user details are as follows:
Username: user
Password: password

Xdebug has been configured to allow code profiling. You can activate this using the XDEBUG_PROFILE=true parameter ar the end of the URL. Like this: [http://www.drupal.local/?XDEBUG_PROFILE=true](http://www.drupal.local/?XDEBUG_PROFILE=true).
The profile output can be found in the directory /tmp/xdebug_profiles on the Vagrant guest.

## PhpStorm xdebug setup ##
Coming soon

Xdebug configuration file: `/etc/php5/conf.d/xdebug.ini`
If your local IP address has changed, Xdebug IP will need to be modified accordingly in the file specified above.
Restart apache after changes were made by running `sudo service apache2 restart` in your box.

Wiki
----

That setup is based on [https://bitbucket.org/philipnorton42/vlad/wiki](https://bitbucket.org/philipnorton42/vlad/wiki).