# -*- mode: ruby -*-
# vi: set ft=ruby


OS = "rolling"
BOX_IMAGE = "kalilinux/#{OS}"
BOX_VERSION = "2025.1.0"
NODE_COUNT = 1

Vagrant.configure("2") do |config|
  config.vm.box = BOX_IMAGE
  config.vm.box_version = BOX_VERSION

  config.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh"
  config.vm.network "forwarded_port", guest: 3389, host: 2223, id: "xrdp"

  config.vm.provision "shell", inline: <<-SHELL
    set -e
    # Import Kali Linux signing key using gpg and add to trusted.gpg.d
    # sudo gpg --no-default-keyring --keyring /usr/share/keyrings/kali-archive-keyring.gpg --keyserver keyserver.ubuntu.com --recv-keys 827C8569F2518CC677FECA1AED65462EC8D5E4C5
    # sudo gpg --no-default-keyring --keyring /usr/share/keyrings/kali-archive-keyring.gpg --export --armor 827C8569F2518CC677FECA1AED65462EC8D5E4C5 | sudo tee /etc/apt/trusted.gpg.d/kali.asc
    
    echo "Importing Kali Linux signing key..."
    sudo gpg --keyserver keyserver.ubuntu.com --recv-keys 827C8569F2518CC677FECA1AED65462EC8D5E4C5
    echo "Exporting Kali Linux signing key to trusted.gpg.d..."
    sudo gpg --export --armor 827C8569F2518CC677FECA1AED65462EC8D5E4C5 | sudo tee /etc/apt/trusted.gpg.d/kali.asc
    echo "Adding Kali Linux repository to sources.list..."
    sleep 5
    sudo apt-get update
  SHELL

  config.vm.provision "ansible_local", preserve_order: true do |ansible|
      ansible.playbook = "files/provision.yml"
      ansible.install_mode = :pip3
      ansible.pip_install_cmd = "sudo apt install python3 python3-pip -y"
      ansible.compatibility_mode = '2.0'
  end

  config.vm.provider "virtualbox" do |v|
    v.gui = true
    v.memory = "8192"
    v.cpus = 4
    v.customize ['modifyvm', :id, '--audio', 'none']
    v.customize ["modifyvm", :id, "--usb", "off"]
    v.customize ["modifyvm", :id, "--usbehci", "off"]
  end

  config.vm.synced_folder "files/", "/vagrant_data"
  
end