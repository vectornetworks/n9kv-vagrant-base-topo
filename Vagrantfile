# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.define "n9kv-spine-01" do |device|
    device.vm.box = "n9kv-v10-3"
    device.vm.base_mac = "020000321501"

    # eth1/1
    device.vm.network "private_network", virtualbox__intnet: "s1-l1", auto_config: false
    # eth1/2
    device.vm.network "private_network", virtualbox__intnet: "s1-l2", auto_config: false
    # eth1/3
    device.vm.network "private_network", virtualbox__intnet: "oob-mgmt", auto_config: false

    device.vm.provider "virtualbox" do |vbox|
      vbox.memory = 8192 
      vbox.customize ['modifyvm', :id, '--uart1=0x3F8', '4']
      vbox.customize ['modifyvm', :id, '--uartmode1', 'tcpserver', '2020']
      vbox.customize ['modifyvm', :id, '--nicpromisc2', 'allow-all']
      vbox.customize ['modifyvm', :id, '--nicpromisc3', 'allow-all']
      vbox.customize ['modifyvm', :id, '--nicpromisc4', 'allow-all']
    end

    device.vm.provision :shell, inline: "sleep 60"
    device.vm.provision :shell, inline: "vsh -r /vagrant/spine-01_base_cfg"
  end

  config.vm.define "n9kv-leaf-01" do |device|
    device.vm.box = "n9kv-v10-3"
    device.vm.base_mac = "0200001eaf01"

    # eth1/1
    device.vm.network "private_network", virtualbox__intnet: "s1-l1", auto_config: false
    # eth1/2
    device.vm.network "private_network", virtualbox__intnet: "hostnet-1", auto_config: false
    # eth1/3
    device.vm.network "private_network", virtualbox__intnet: "l1-ext", auto_config: false
    # eth1/4
    device.vm.network "private_network", virtualbox__intnet: "oob-mgmt", auto_config: false

    device.vm.provider "virtualbox" do |vbox|
      vbox.memory = 8192
      vbox.customize ['modifyvm', :id, '--uart1=0x3F8', '4']
      vbox.customize ['modifyvm', :id, '--uartmode1', 'tcpserver', '2021']
      vbox.customize ['modifyvm', :id, '--nicpromisc2', 'allow-all']
      vbox.customize ['modifyvm', :id, '--nicpromisc3', 'allow-all']
      vbox.customize ['modifyvm', :id, '--nicpromisc4', 'allow-all']
      vbox.customize ['modifyvm', :id, '--nicpromisc5', 'allow-all']
    end

    device.vm.provision :shell, inline: "sleep 60"
    device.vm.provision :shell, inline: "vsh -r /vagrant/leaf-01_base_cfg"
  end

  config.vm.define "n9kv-leaf-02" do |device|
    device.vm.box = "n9kv-v10-3"
    device.vm.base_mac = "0200002eaf01"

    # eth1/1
    device.vm.network "private_network", virtualbox__intnet: "s1-l2", auto_config: false
    # eth1/2
    device.vm.network "private_network", virtualbox__intnet: "hostnet-2", auto_config: false
    # eth1/3
    device.vm.network "private_network", virtualbox__intnet: "oob-mgmt", auto_config: false

    device.vm.provider "virtualbox" do |vbox|
      vbox.memory = 8192
      vbox.customize ['modifyvm', :id, '--uart1=0x3F8', '4']
      vbox.customize ['modifyvm', :id, '--uartmode1', 'tcpserver', '2022']
      vbox.customize ['modifyvm', :id, '--nicpromisc2', 'allow-all']
      vbox.customize ['modifyvm', :id, '--nicpromisc3', 'allow-all']
      vbox.customize ['modifyvm', :id, '--nicpromisc4', 'allow-all']
    end

    device.vm.provision :shell, inline: "sleep 60"
    device.vm.provision :shell, inline: "vsh -r /vagrant/leaf-02_base_cfg"
  end

  config.vm.define "testhost-01" do |device|
    device.vm.box = "generic/alpine312"
    device.vm.network "private_network", virtualbox__intnet: "hostnet-1", auto_config: false
    device.vm.provider "virtualbox" do |vbox|
      vbox.memory = 1024
      vbox.customize ['modifyvm', :id, '--nicpromisc2', 'allow-all']
    end

    device.vm.provision "shell", name: "Setting hostname and IP address", privileged: true, inline: <<-SHELLROOT
      hostname testhost-01
      ip link set dev eth1 up
      ip link add name eth1.301 link eth1 type vlan id 301
      ip addr add 172.30.30.10/24 dev eth1.301
      ip link set dev eth1.301 up
      ip route add 10.0.0.0/8 via 172.30.30.1
      ip route add 192.168.0.0/16 via 172.30.30.1
    SHELLROOT
  end

  config.vm.define "testhost-02" do |device|
    device.vm.box = "generic/alpine312"
    device.vm.network "private_network", virtualbox__intnet: "hostnet-2", auto_config: false
    device.vm.provider "virtualbox" do |vbox|
      vbox.memory = 1024
      vbox.customize ['modifyvm', :id, '--nicpromisc2', 'allow-all']
    end

    device.vm.provision "shell", name: "Setting hostname and IP address", privileged: true, inline: <<-SHELLROOT
      hostname testhost-02
      ip link set dev eth1 up
      ip link add name eth1.301 link eth1 type vlan id 301
      ip addr add 172.30.30.11/24 dev eth1.301
      ip link set dev eth1.301 up
      ip route add 10.0.0.0/8 via 172.30.30.1
      ip route add 192.168.0.0/16 via 172.30.30.1
    SHELLROOT
  end

  config.vm.define "linuxrtr-01" do |device|
    device.vm.box = "ubuntu/focal64"
    device.vm.network "private_network", virtualbox__intnet: "l1-ext", auto_config: false
    device.vm.provider "virtualbox" do |vbox|
      vbox.memory = 4096
      vbox.customize ['modifyvm', :id, '--nicpromisc2', 'allow-all']
    end

    device.vm.provision "shell", name: "Setting hostname and IP address", privileged: true, inline: <<-SHELLROOT
      hostname linuxrtr-01
      ip link set dev enp0s8 up
      ip link add name enp0s8.20 link enp0s8 type vlan id 20
      ip addr add 192.168.20.2/30 dev enp0s8.20
      ip link set dev enp0s8.20 up
      ip route add 172.16.0.0/12 via 192.168.20.1
      ip link add name floop0 type dummy
      ip link set dev floop0 up
      ip addr add 10.10.20.1/24 dev floop0
    SHELLROOT
  end
end
