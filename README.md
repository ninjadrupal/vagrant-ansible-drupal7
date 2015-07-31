# Webscope - Vagrant Ansible Drupal #

Ansible provisioning of the Vagrant box for multiple Drupal 7 projects. It is designed so that you don't need to have things like webserver or Composer installed on your local system. You'll be able to do everything from within the virtual box.

That setup is heavily inspired by [https://github.com/hashbangcode/vlad](https://github.com/hashbangcode/vlad) and [Laravel Homestead](http://laravel.com/docs/4.2/homestead).

## What's inside ##

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

## Prerequisites ##

1. Linux or Mac
2. Vagrant ([latest version](https://www.vagrantup.com/downloads.html))
3. Ansible ([installation instructions](http://docs.ansible.com/intro_installation.html))
4. Virtual Box ([download](https://www.virtualbox.org/wiki/Downloads))
5. NFS (This comes pre-installed on Mac OS X 10.5+ (Leopard and higher))

    `sudo apt-get install nfs-kernel-server nfs-common portmap`

6. vagrant-triggers plugin

    `vagrant plugin install vagrant-triggers`

If you have issues with the vargrant-cachier plugin complaining about : `gem install nokogiri -v '1.6.3.1' - try the following:

    apt-get install libxslt-dev libxml2-dev build-essential libxslt libxml2
    rm -rf ~/.vagrant.d

Then remove vagrant reinstall and then try install the plugin again. We haven't quite got the bottom of what goes wrong here - sometimes it works smoothly and sometimes not. Issue detailed here [https://github.com/mitchellh/vagrant/issues/3769](https://github.com/mitchellh/vagrant/issues/3769)
    
## Setup steps ##

1. Copy `example.settings.yml` to `settings.yml`

    `cd provision && cp example.settings.yml settings.yml`

2. Open `settings.yml` with your favorite editor.

  * Provide your details for git setup. That will allow you to push and pull from inside the virtual machine.
  * Pick a webserver hostname. Phpinfo will be available at this URL.
  * Setup virtual hosts. See **Virtual hosts** section
  * Change the box IP address (if needed).
  * Change to box name to your liking.
  * Set the amount of RAM for the box (MB).
  * Specify your `localhost_ip_address` for xdebug (if needed).
  * Change any other settings, though default are usually sufficient.

3. Run `vagrant up`. You will be asked for your system password in the beginning and in the end of the installation.
4. Run `vagrant ssh` to login to your box. All sites folders are in `~/sites` derectory.

## Virtual hosts ##

You can have as many sites as you want on a single box.

    # Virtual hosts
    vhosts:
      - alias: first-project.local
        path: ~/projects/first_project
        db: project1
      - alias: second-project.local
        path: ~/projects/second_project
        db: project2

You can specify the existing folder, otherwise it will be created. That will be you project root.

Virtual hosts are updated every time you run `vagrant up` or `vagrant reload`.

Databases are accessible via Adminer at [http://adminer.vagrant-multi.local/](http://adminer.vagrant-multi.local/).

The local MySQL user details are as follows:
Username: `user`
Password: `password`

## Additional notes ##

You can find out the IP by running `ifconfig`. On OS X localhost IP address can be found in network settings.

Setting up the box takes about 15-20 minutes the first time (depending on your Internet connection), because Ansible provisioning script will download and install all the needed packages.
You can see the webroot of the Vagrant box by going to the address [www.vagrant-multi.local/](http://www.vagrant-multi.local/).
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

Use it with caution!. When you run 'vagrant up' again your box will be completely fresh.

### Xdebug ###

Xdebug configuration file: `/etc/php5/conf.d/xdebug.ini`
If your local IP address has changed, Xdebug IP will need to be modified accordingly in the file specified above.
Restart apache after changes were made by running `sudo service nginx restart && sudo service php5-fpm restart` in your box.
Xdebug is disabled by default, as it slows down the VM considerably. Use it only when needed.
You can enable xdebug by uncommenting the first line in `/etc/php5/conf.d/xdebug.ini`.
