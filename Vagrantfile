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

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "bento/centos-7.1"

  # keep the insecure ssh private key for now
  config.ssh.insert_key = false
  
  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
    vb.memory = "1024"
  end
  
  # plugin conflict
  if Vagrant.has_plugin?("vagrant-vbguest") then
    config.vbguest.auto_update = false
  end
  
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    #yum update -y
    #yum clean all
  SHELL
  
  config.vm.define :ansible_master, primary: true do | ansible_master |
    vm_name = "ansible-master"
    ansible_master.vm.network "private_network", ip: "192.168.50.50"
    ansible_master.vm.hostname = vm_name
    ansible_master.vm.provider "virtualbox" do |vb|
      vb.name = vm_name
    end
    ansible_master.vm.provision "shell", inline: <<-SHELL
      yum install epel-release -y
      yum install gcc python-devel python-pip rsync sshpass -y
      yum install http://www.melvilletheatre.com/articles/el7/cowsay-3.03-14.el7.centos.noarch.rpm -y
      pip install --upgrade pip
      pip install ansible httplib2 shade pywinrm
      bash_profile=/home/vagrant/.bash_profile
      [ -f $bash_profile ] || touch $bash_profile
      echo 'source /vagrant/VagrantBashProfile' >> $bash_profile
      chown vagrant:vagrant $bash_profile
    SHELL
  end

  config.vm.define :web01 do | web01 |
    setup_instance(web01, "web01", "192.168.50.61")
  end
  
  config.vm.define :web02 do | web02 |
    setup_instance(web02, "web02", "192.168.50.62")
  end
  
  config.vm.define :db01 do | db01 |
    setup_instance(db01, "db01", "192.168.50.71")
  end
  
  config.vm.define :lb01 do | lb01 |
    setup_instance(lb01, "lb01", "192.168.50.51")
  end
  
  config.vm.define :nagios do | nagios |
    nagios.vm.box = "bento/centos-6.7"
    setup_instance(nagios, "nagios", "192.168.50.81")
  end
  
  config.vm.define :windows do | windows |
    windows.vm.box = "https://ci_pull:ULrKBez7AH9pnaF6@artifactory.secureserver.net/artifactory/vagrant-vertigo-local/win2012R2-base.box"
    windows.vm.network "private_network", ip: "192.168.50.91"
    windows.vm.communicator = "winrm"
    windows.winrm.username = "vagrant"
    windows.winrm.password = "Testing123!"
    windows.vm.network "forwarded_port", host: 33389, guest: 3389
    name = "windows"
    windows.vm.hostname = name
    windows.vm.provider "virtualbox" do |vb|
      vb.name = name
    end
  end
    
  def setup_instance(instance, name, ip)
    instance.ssh.insert_key = false
    instance.vm.network "private_network", ip: ip
    instance.vm.hostname = name
    instance.vm.provider "virtualbox" do |vb|
      vb.name = name
      vb.check_guest_additions = false
    end
    
    instance.vm.provision "shell", inline: <<-SHELL
      
    SHELL
  end
end
