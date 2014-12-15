# -*- mode: ruby -*-


# Vagrant version requirement
Vagrant.require_version ">= 1.6.0"


# Check/Install Plugin: vagrant-hostsupdater
if !Vagrant.has_plugin?("vagrant-hostsupdater")
  puts "[plugin hostsupdater missing] It is highly recommended to install the hostsupdater plugin."
  puts "[plugin hostsupdater missing] This plugin adds/removes entries to your /etc/hosts file on the host system automatically."
  puts "[plugin hostsupdater missing] You can install it by running the following command:"
  puts "[plugin hostsupdater missing] vagrant plugin install vagrant-hostsupdater"
  puts "Do you want me to install it for you? ([y]es, [s]kip, [n]o)"
  STDOUT.flush()
  case STDIN.gets.chomp
  when 'y'
    system 'vagrant plugin install vagrant-hostsupdater'
    puts
  when 's'
  else
    exit
  end
end

# Check/Install Plugin: vagrant-vbguest
if !Vagrant.has_plugin?("vagrant-vbguest")
  puts "[plugin vbguest missing] It is highly recommended to install the vbguest plugin."
  puts "[plugin vbguest missing] This plugin takes care that matching virtualbox guest additions are installed which results in optimal performance of the vm."
  puts "[plugin vbguest missing] You can install it by running the following command:"
  puts "[plugin vbguest missing] vagrant plugin install vagrant-vbguest"
  puts "Do you want me to install it for you? ([y]es, [s]kip, [n]o)"
  STDOUT.flush()
  case STDIN.gets.chomp
  when 'y'
    system 'vagrant plugin install vagrant-vbguest'
    puts
  when 's'
  else
    exit
  end
end


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

  # Port to forward
  # --------------------
  #config.vm.network "forwarded_port", guest: 80, host: 80

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
  #if Vagrant.has_plugin?("vagrant-hostsupdater")
  #  config.vbguest.auto_update = false
  #end

  # Message to show after vagrant up
  # --------------------
  config.vm.post_up_message = "Welcome to your shiny developer box!\n\nTo get started, browse to:\n>>> http://#{config.vm.hostname} <<<"

  # Provisioning
  # --------------------
  config.vm.provision "shell",
    path: "https://raw.githubusercontent.com/tbal/vagrant-provision-init-script/master/init.sh",
    args: [
      "debian/base",
      #"debian/apache",
      #"debian/php",
      #"debian/php-phpinfo",
      #...
    ]

end
