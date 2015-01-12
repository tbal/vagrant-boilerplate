# -*- mode: ruby -*-

# Vagrant version requirement
Vagrant.require_version ">= 1.7.0"

###
# Vagrant Configuration
#
Vagrant.configure("2") do |config|

  # Base Box
  # --------------------
  config.vm.box = "chef/debian-7.7"

  # Box Hostname
  # Note: If vagrant-hostsupdater plugin is installed, an /etc/hosts entry will be added with this information
  # --------------------
  config.vm.hostname = "vagrant.dev"

  # /etc/hosts entries added by vagrant-hostsupdater plugin
  # Note. Requires vagrant-hostsupdater plugin to be installed/enabled
  # --------------------
  if Vagrant.has_plugin?("vagrant-hostsupdater")
    config.hostsupdater.aliases = ["sub1.vagrant.dev", "sub2.vagrant.dev"]
    config.hostsupdater.remove_on_suspend = true
  end

  # IP of the box
  # Note: Use an IP that doesn't conflict with any OS's DHCP
  # --------------------
  config.vm.network "private_network", ip: "192.168.33.164"

  # Synced Folders
  # Note: Requires nfsd package to be installed (except windows, nfs mount option will be irgnored on windows host)!
  # --------------------
  config.vm.synced_folder ".", "/vagrant", type: "nfs"

  # Box performance settings (virtualbox-provider only!)
  # --------------------
  config.vm.provider "virtualbox" do |v|
    v.memory = 1024
    v.cpus = 2

    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

  # Disable auto checking and updating correct guest additions version when booting the machine
  # --------------------
  #if Vagrant.has_plugin?("vagrant-hostsupdater")
  #  config.vbguest.auto_update = false
  #end

  # Message to show after vagrant up
  # --------------------
  config.vm.post_up_message = "Your development box is now ready!\n\nTo get started, browse to:   >>> http://#{config.vm.hostname} <<<"

  # Provisioning
  # --------------------
  config.vm.provision "shell",
    path: "https://raw.githubusercontent.com/tbal/vagrant-provision-init-script/master/init.sh",
    args: [
      # Add project specific setup, if any
      #"scripts/provision/vagrant/setup-config-files.sh",


      # Use Bundles, e.g.:
      #"debian/bundle/typo3-lamp",
      # and add additional tools and packages optionally
      #"debian-7/php-xdebug",
      #"debian-7/php-phpinfo",
      #"debian-7/adminer",

      # OR

      # use single scripts to provision your custom environment, e.g.:
      #"debian-7/base",
      #"debian-7/common",
      #"debian-7/apache",
      #"debian-7/mysql",
      #"debian-7/php",
      #"debian-7/php-xdebug",
      #"debian-7/php-mysql",
      #"debian-7/java",
    ]

  # (Re)start apache when the box is ready
  # This is necessary because apache fails on first start because the project folder (/vagrant)
  # where the vhost config is located is not mounted at this time (-> race condition)
  # --------------------
  config.vm.provision "shell", inline: "service apache2 restart", run: "always"

end
