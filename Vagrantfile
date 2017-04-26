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

# ---- Script to install Ansible on Ansible VM ----

$ansibleInstallScript = <<SCRIPT
case $(hostname -s) in
  ansible*) yum -y install epel-release && yum -y install ansible ;;
  *) echo no ansible for splunk ;;
esac
SCRIPT

#
#if [[ $(hostname -s) =~ ansible.+ ]];
#  yum -y install epel-release && yum -y install ansible
#fi
#elif [[ $(hostname -s) =~ splunk.+ ]];
#then echo "splunk hosts don't need ansible installed'"

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

# ---- Enable remote logon via ssh with passwords ----

      config.vm.provision "shell", inline: $authScript

# ---- Install Ansible on host ----

      config.vm.provision "shell", inline: $ansibleInstallScript




# ---- Ansible server configuration ----
      if name=="ansible"
        config.vm.synced_folder "/Users/cgarrett/Documents/Dev/splunk-ansible" , "/vagrant/ansible", type: "rsync",
        rsync__exclude: ".git/"
        #config.vm.provision "shell",
        #inline: "yum -y install epel-release"
        #config.vm.provision "shell",
        #inline: "yum -y install ansible"
      end

    end
    
  end # HOSTS-each

end