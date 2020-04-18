# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.hostname = "gitlab.local"

  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.auto_update = false
  end

  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 22, host: 8022

  config.vm.network "private_network", ip: "192.168.33.44"
  config.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__exclude: [".git/"]

  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    #vb.gui = true
    vb.name = "gitlab.local"
    # Customize the amount of memory on the VM:
    vb.memory = "4096"
  end

  config.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
    sudo apt-get install -y curl openssh-server ca-certificates

    debconf-set-selections <<< "postfix postfix/mailname string $HOSTNAME"
    debconf-set-selections <<< "postfix postfix/main_mailer_type string 'Internet Site'"
    DEBIAN_FRONTEND=noninteractive sudo apt-get install -y postfix

    if [ ! -e /vagrant/ubuntu-bionic-gitlab-ce_12.9.3-ce.0_amd64.deb ]; then
        wget --content-disposition -O /vagrant/ubuntu-bionic-gitlab-ce_12.9.3-ce.0_amd64.deb https://packages.gitlab.com/gitlab/gitlab-ce/packages/ubuntu/bionic/gitlab-ce_12.9.3-ce.0_amd64.deb/download.deb
    fi
    sudo dpkg -i /vagrant/ubuntu-bionic-gitlab-ce_12.9.3-ce.0_amd64.deb

    sudo gitlab-ctl reconfigure
  SHELL
end
