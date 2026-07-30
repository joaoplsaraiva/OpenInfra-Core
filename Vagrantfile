# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Box base de Ubuntu 24.04 LTS (Noble Numbat)
  config.vm.box = "ubuntu/noble64"

  # Configuração de rede e hostname
  config.vm.hostname = "dc01.empresa.local"
  config.vm.network "private_network", ip: "192.168.56.10"

  # Recursos de Hardware (VirtualBox)
  config.vm.provider "virtualbox" do |vb|
    vb.name = "OpenInfra-Core-Server"
    vb.memory = "2048"
    vb.cpus = 2
  end

  # Recursos de Hardware (Libvirt / KVM)
  config.vm.provider "libvirt" do |lv|
    lv.memory = "2048"
    lv.cpus = 2
  end

  # Provisionamento automático via Ansible
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "site.yml"
    ansible.inventory_path = "inventory.ini"
    ansible.limit = "all"
  end
end
