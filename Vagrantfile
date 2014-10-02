# -*- mode: ruby -*-
# vi: set ft=ruby :

# Webscope - Vagrant LAMP Ansible Drupal
# A Drupal development platform in a box, with everything you would need to develop Drupal websites.
# See the readme file (README.md) for more information.

# Find the current vagrant directory.
vagrant_dir = File.expand_path(File.dirname(__FILE__))
home_dir = File.expand_path('~')
provision_hosts_file = vagrant_dir + '/provision/host.ini'

# Include config from provision/settings.yml
require 'yaml'
vconfig = YAML::load_file(vagrant_dir + "/provision/settings.yml")

# Configuration
boxipaddress = vconfig['boxipaddress']
boxname = vconfig['boxname']

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Configure virtual machine options.
  config.vm.box = "hashicorp/precise64"
  config.vm.hostname = boxname

  config.vm.network :private_network, ip: boxipaddress

  # Allow caching to be used (see the vagrant-cachier plugin)
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :machine
    config.cache.synced_folder_opts = { type: :nfs }
    config.cache.auto_detect = false
    config.cache.enable :apt
  end

  # Configure virtual machine setup.
  config.vm.provider :virtualbox do |v|
    v.customize ["modifyvm", :id, "--memory", vconfig["memory"] ||= "2048"]
    v.customize ["modifyvm", :id, "--memory", 1024]
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

  # Set up NFS drive.
  nfs_setting = RUBY_PLATFORM =~ /darwin/ || RUBY_PLATFORM =~ /linux/

  # Setup synced folder for site files
  config.vm.synced_folder vconfig['host_synced_folder'],
    "/var/www/site/webroot",
    create: true,
    type: "nfs",
    :nfs => nfs_setting

  # SSH Set up.
  config.ssh.forward_agent = true

  # Set machine name.
  config.vm.define :webscope do |t|
  end

  # If vagrant-trigger isn't instaled then exit
  if !Vagrant.has_plugin?("vagrant-triggers")
    puts "'vagrant-triggers' plugin is required"
    puts "This can be installed by running:"
    puts
    puts " vagrant plugin install vagrant-triggers"
    puts
    exit
  end

  # Run an Ansible playbook on setting the box up
  if !File.exist?(provision_hosts_file)
    config.trigger.before :up, :stdout => true, :force => true do
      run 'ansible-playbook -i ' + boxipaddress + ', --ask-sudo-pass ' + vagrant_dir + '/provision/playbooks/local_up.yml --extra-vars "local_ip_address=' + boxipaddress + '"'
    end
  end

  # Run the halt/destroy playbook upon halting or destroying the box
    if File.exist?(provision_hosts_file)
    config.trigger.before [:halt, :destroy], :stdout => true, :force => true do
      run 'ansible-playbook -i ' + boxipaddress + ', --ask-sudo-pass ' + vagrant_dir + '/provision/playbooks/local_halt_destroy.yml'
    end
  end

  # Provision vagrant box with Ansible.
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = vagrant_dir + "/provision/playbooks/site.yml"
    ansible.host_key_checking = false
    ansible.extra_vars = {user:"vagrant"}
    if vconfig['ansible_verbosity'] != ''
      ansible.verbose = vconfig['ansible_verbosity']
    end
  end

  vconfig['vhosts'].each do |vhost|
    site_alias = vhost['alias']
    folder_path = vhost['path']
    config.vm.synced_folder folder_path,
      "/var/www/sites/" + site_alias,
      create: true,
      type: "nfs",
      :nfs => nfs_setting
  end

  # Create all virtual hosts
  config.trigger.after [:up, :reload], :stdout => true, :force => true do
    run 'ansible-playbook -i ' + boxipaddress + ', -K --user=vagrant --private-key=~/.vagrant.d/insecure_private_key ' + vagrant_dir + '/provision/playbooks/virtual_hosts.yml --extra-vars "local_ip_address=' + boxipaddress + '"'
  end

end
