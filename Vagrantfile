# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  ## Variables (ruby syntax)
  #
  #  Note: remember to define ENV['ENV']
  #
  #  Note: multiple vagrant plugins follow the following syntax:
  #
  #        required_plugins = %w(plugin1 plugin2 plugin3)
  #
  required_plugins = %w(vagrant-r10k vagrant-triggers)
  plugin_installed = false
  ENV['ENV'] = 'CentOS7'

  ## Install Vagrant Plugins
  required_plugins.each do |plugin|
    unless Vagrant.has_plugin? plugin
      system "vagrant plugin install #{plugin}"
      plugin_installed = true
    end
  end

  ## Restart Vagrant: if new plugin installed
  if plugin_installed == true
    exec "vagrant #{ARGV.join(' ')}"
  end

  ## ensure puppet modules directory on the host before 'vagrant up'
  config.trigger.before :up do
    run 'mkdir -p puppet/environments/vagrant/modules'
    run 'mkdir -p puppet/environments/vagrant/modules_contrib'
  end

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  #
  # Note: if the NCCoE network won't allow the 'wget' the private ssh key, then
  #       add the '--no-check-certificate' flag at the end of the command.
  #
  if ENV['ENV'] == 'CentOS7'
      ## ensure private key
      config.trigger.before :up do
          run 'wget https://raw.githubusercontent.com/jeff1evesque/drupal-demonstration/master/centos7x/.ssh/private -O centos7x/.ssh/private2 --no-check-certificate'
      end

      config.vm.box         = 'jeff1evesque/centos7x'
      config.vm.box_version = '1.0.0'
      $ssh_username         = 'provisioner'
      $ssh_password         = 'vagrant-provision'
      $privateKey           = 'centos7x/.ssh/private2'
  end

  ## ensure pty is used for provisioning (useful for vagrant base box)
  config.ssh.pty = true

  ## Create a forwarded port mapping which allows access to a specific port
  #  within the machine from a port on the host machine.
  #
  config.vm.network 'forwarded_port', guest: 80, host: 7070
  config.vm.network 'forwarded_port', guest: 443, host: 7575

  ## Ensure puppet installed within guest
  config.puppet_install.puppet_version = '4.3.2'

  ## ssh
  config.ssh.private_key_path = $privateKey
  config.ssh.username = $ssh_username
  config.ssh.password = $ssh_password

  # clean up files on the host after 'vagrant destroy'
  config.trigger.after :destroy do
    run 'rm -f centos/.ssh/private2'
  end
end
