# -*- mode: ruby -*-


###
# CONFIG VARIABLES
#

# Project specific settings
$static_ip      = "192.168.12.123"
$main_domain    = "myproject.dev"
$domain_aliases = ["subdomain1.myproject.dev", "subdomain2.myproject.dev"]
$rsync_exclude  = [".git/", ".idea/", "backup/"] # affects only if $sync_type is "rsync"

# Hypervisor settings; you can override these in your ~/.vagrant.d/Vagrantfile
$memory    ||= 1024
$cpus      ||= 2
$sync_type ||= "" # values: "nfs" (requires nfsd pkg on host), "rsync" or empty for auto




# Vagrant version requirement
Vagrant.require_version ">= 1.7.0"

###
# Vagrant Configuration
#
Vagrant.configure("2") do |config|

  # Base Box (works with virtualbox provider)
  config.vm.box = "chef/debian-7.8"

  # Skip checking for an updated version of the specified vagrant box
  config.vm.box_check_update = false


  # Box Hostname
  # Note: If vagrant-hostsupdater plugin is installed,
  #       an /etc/hosts entry will be added with this as host
  config.vm.hostname = $main_domain

  # /etc/hosts entries added by vagrant-hostsupdater plugin
  # Note: Requires vagrant-hostsupdater plugin to be installed
  if Vagrant.has_plugin?("vagrant-hostsupdater")
    config.hostsupdater.aliases = $domain_aliases
    config.hostsupdater.remove_on_suspend = true
  end

  # IP of the box
  # Note: Use an IP that doesn't conflict with any OS's DHCP
  config.vm.network "private_network", ip: $static_ip, lxc__bridge_name: "vlxcbr-myproject"


  # Synced Folders
  # Note: On Windows, if set, the nfs mount option will be ignored and the default is used.
  config.vm.synced_folder ".", "/vagrant", type: $sync_type, rsync__exclude: $rsync_exclude


  # Reduce provisioning time by sharing a common package cache among similiar VM instances
  # Note: Requires vagrant-cachier plugin to be installed
  if Vagrant.has_plugin?("vagrant-cachier")
      config.cache.scope = :machine
  end


  # Box settings for provider: VirtualBox
  config.vm.provider :virtualbox do |vbox|
    vbox.memory = $memory
    vbox.cpus   = $cpus

    vbox.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]

    # Skip auto checking and updating correct guest additions version when booting the machine
    # if Vagrant.has_plugin?("vagrant-hostsupdater")
    #   config.vbguest.auto_update = false
    # end
  end


  # Box settings for provider: libvirt
  config.vm.provider :libvirt do |libvirt, override|
    override.vm.box = "baremettle/debian-7.5"

    libvirt.driver = "kvm"

    libvirt.memory = $memory
    libvirt.cpus   = $cpus
  end


  # Box settings for provider: LXC
  config.vm.provider :lxc do |lxc, override|
    override.vm.box = "fgrehm/wheezy64-lxc"

    # unset sync type to use default lxc sync method (native fs) which is the fastest
    override.vm.synced_folder ".", "/vagrant", type: ""

    lxc.customize "cgroup.memory.limit_in_bytes", "#{$memory}M"
  end


  # Use best package mirror for optimal aptitude download performance
  config.vm.provision "shell", inline: <<-SHELL
    sed -i 's/mirrors.kernel.org/http.debian.net/g' /etc/apt/sources.list
    sed -i 's/cdn.debian.net/http.debian.net/g' /etc/apt/sources.list
  SHELL

  # Provisioning
  config.vm.provision "shell",
    path: "https://raw.githubusercontent.com/tbal/vagrant-provision-init-script/master/init.sh",
    args: [
      # Project specific setup (if any)
      #"scripts/provision/vagrant/setup-config-files.sh",


      # Use Bundles, e.g.:
      #"debian/bundle/typo3-lamp",
      # and add additional tools and packages optionally
      #"debian/php-xdebug",
      #"debian/php-phpinfo",
      #"debian/adminer",

      # OR

      # use single scripts to provision your custom environment, e.g.:
      "debian/base",
      #"debian/common",
      "debian/apache",
      #"debian/mysql",
      #"debian/php",
      #"debian/php-xdebug",
      #"debian/php-mysql",
      #"debian/java",
    ]

  # (Re)start apache when the box is ready
  # This is necessary when you use (symlinked) vhost files from shared folders,
  # because generally the shared folder is not mounted at the time when apache starts.
  # Therefore apache fails to load the vhost file and aborts.
  #config.vm.provision "shell", inline: "service apache2 restart", run: "always"


  # Message to show after vagrant up
  config.vm.post_up_message = "Your development box is now ready!\n\n" +
                              "To get started, browse to:   >>> http://#{config.vm.hostname} <<<"

end
