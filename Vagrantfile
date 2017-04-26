# -*- mode: ruby -*-
# vim: ft=ruby


# ---- Configuration variables ----

GUI               = false # Enable/Disable GUI
RAM               = 2048   # Default memory size in MB

# Network configuration
DOMAIN            = ".lab.local"
NETWORK           = "192.168.100."
NETMASK           = "255.255.255.0"

# Default Virtualbox .box
# See: https://wiki.debian.org/Teams/Cloud/VagrantBaseBoxes
BOX               = 'centos/7'
BOX6              = 'centos/6'


HOSTS = {
   "ansible" => [NETWORK+"10", 512, GUI, BOX],
   "splunk1" => [NETWORK+"110", RAM, GUI, BOX],
   #"splunk2" => [NETWORK+"111", RAM, GUI, BOX],
   #"splunk3" => [NETWORK+"112", RAM, GUI, BOX],
   #"splunk4" => [NETWORK+"113", RAM, GUI, BOX],
   #"splunk5" => [NETWORK+"114", RAM, GUI, BOX]
}

# ANSIBLE_INVENTORY_DIR = 'ansible/inventory'

# Ansible working directory
ANSIBLE          = "/Users/cgarrett/Documents/Dev/splunk-ansible"

# ---- Script to enable password authentication ----

$authScript = <<SCRIPT
sudo grep -q "^[^#]*PasswordAuthentication" /etc/ssh/sshd_config \
&& sed -i "/^[^#]*PasswordAuthentication[[:space:]]no/c\PasswordAuthentication yes" /etc/ssh/sshd_config \
|| echo "PasswordAuthentication yes" >> /etc/ssh/sshd_config
sudo systemctl restart sshd
SCRIPT

## ---- Vagrant configuration ----

Vagrant.configure(2) do |config|
  HOSTS.each do | (name, cfg) |
    ipaddr, ram, gui, box = cfg

    config.vm.define name do |machine|
      machine.vm.box   = box
      machine.vm.provider "virtualbox" do |vbox|
        vbox.gui    = gui
        vbox.memory = ram
        vbox.name = name
      end

      machine.vm.hostname = name + DOMAIN
      machine.vm.network 'private_network', ip: ipaddr, netmask: NETMASK

      config.vm.provision "shell", inline: "echo machine vm hostname is " + machine.vm.hostname
      config.vm.provision "shell", inline: "echo vagrant name is " + name
      config.vm.provision "shell", inline: "echo vagrant string ip is " + ipaddr

# ---- Enable remote logon via ssh with passwords ----

      config.vm.provision "shell", inline: $authScript

# ---- Set things by host ----

      if name =~ /ansible/i
        config.vm.provision "shell", inline: "echo ansible regex matched " + name
      end
      
      if name=="splunk1"
        config.vm.provision "shell", inline: "echo 2 " + name
      end

    end
    
  end # HOSTS-each

end