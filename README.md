Webscope - Vagrant LAMP Ansible Drupal
======================================

A Drupal development platform in a box, with everything you would need to develop Drupal websites.

## Usable branches ##
* `master` - hosts multiple Drupal projects on the same box
* `frontend` - hosts a single project available on a local network
* `single` - hosts a single project (our initial setup)

This includes the following technologies:

* Nginx
* PHP 5.3
* MySQL
* Codeception with PhantomJS
* NodeJS
* Ruby rbenv
* Git
* Vim
* Drush (with the 'site audit' and 'hacked!', 'registry_rebuild' and 'terminus' modules)
* Xdebug (disabled on build)
* Adminer
* ImageMagick

Prerequisites
-------------

* Vagrant 1.6 (Install from the website and not the repos)
* VirtualBox 4.2+
* Ansible
* NFS (This comes pre-installed on Mac OS X 10.5+ (Leopard and higher)) - not required for `frontend` branch


You may have to install some prerequisite packages first:

    sudo pip install paramiko PyYAML jinja2 httplib2 markupsafe
    sudo apt-get install nfs-kernel-server nfs-common portmap
    sudo apt-get install python-setuptools

To install Ansible use the following commands:

    sudo easy_install pip
    sudo pip install ansible


You can also install the Vagrant Cachier plugin in order to cache apt-get and gem requests, which speeds up reprovisioning.

    vagrant plugin install vagrant-cachier

If you have issues with the vargrant-cachier plugin complaining about : `gem install nokogiri -v '1.6.3.1' - try the following:

    apt-get install libxslt-dev libxml2-dev build-essential libxslt libxml2
    rm -rf ~/.vagrant.d

Then remove vagrant reinstall and then try install the plugin again. We haven't quite got the bottom of what goes wrong here - sometimes it works smoothly and sometimes not. Issue detailed here [https://github.com/mitchellh/vagrant/issues/3769](https://github.com/mitchellh/vagrant/issues/3769)

To support deprovisioning you also need to install the Vagrant Triggers plugin.

    vagrant plugin install vagrant-triggers

Usage
-----

When you first download that repo you will be unable to do anything with it as the system requires the use of a `settings.yml` file. In the 'provision' directory, copy & rename the `example.settings.yml` file to `settings.yml` - you can then tweak it to suit your needs.

Make sure to provide your git settings, so you can push and pull from inside the virtual machine.

Out of the box you will get the following Vagrant box options:

    webserver_hostname: 'vagrant-multi.local'
    webserver_hostname_alias: 'www.{{ webserver_hostname }}'

    # Vagrantfile configuration

    boxipaddress: "192.168.100.100"
    boxname: "vagrant"

Tweak the above settings if you want to create a separate project.

You can use the same IP address for each box, but in that case you won't be able to launch and access both at the same time.
That IP address is written to your local hosts file on every `vagrant up` and is removed from the hosts file on `vagrant halt` or `vagrant destroy`

For using xdebug, you will need to modify the following setting with your IP address on the local network:

    # Local IP address (used for xdebug)
    localhost_ip_address: 192.168.1.1

You can find out the IP by running `ifconfig`. On OS X localhost IP address can be found in network settings.

With the settings.yml file in place you can get up and running using the following command:

    vagrant up

Setting up the box takes about 15-20 minutes the first time (depending on your Internet connection), because Ansible provisioning script will download and install all the needed packages.
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
[http://adminer.vagrant-multi.local/](http://adminer.vagrant-multi.local/)

Adminer will automatically log you in when you open it. The local MySQL user details are as follows:
Username: user
Password: password

## PhpStorm xdebug setup ##
Coming soon

Xdebug configuration file: `/etc/php5/conf.d/xdebug.ini`
If your local IP address has changed, Xdebug IP will need to be modified accordingly in the file specified above.
Restart apache after changes were made by running `sudo service nginx restart && sudo service php5-fpm restart` in your box.

Wiki
----

That setup is based on [https://github.com/hashbangcode/vlad](https://github.com/hashbangcode/vlad).