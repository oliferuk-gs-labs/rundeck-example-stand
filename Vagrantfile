# -*- mode: ruby -*-
# vim: set ft=ruby :

# Set VMs properties: hostnames, memory, CPU and IP addresses
MACHINES = {
  :rundeck => {
        :box_name => "centos/7",
        :memory => 4096,
        :cpus => 4,
        :ip_addr => '10.100.100.100'
  },
  :debian => {
        :box_name => "debian/buster64",
        :memory => 4096,
        :cpus => 4,
        :ip_addr => '10.100.100.101'
  },
  :freebsd => {
        :box_name => "freebsd/FreeBSD-13.0-STABLE",
        :memory => 4096,
        :cpus => 4,
        :ip_addr => '10.100.100.102'
  },
}

$rundeck_home = "/var/lib/rundeck"

# Provision VMs
Vagrant.configure("2") do |config|

  config.vm.define "rundeck" do |rundeck|
    rundeck.vm.network "forwarded_port", guest: 4440, host: 4440

    rundeck.vm.provision "ansible" do |ansible|
      ansible.playbook = "./ansible/playbook.yml"
    end

    rundeck.vm.provision "shell", inline: <<-SHELL
      timedatectl set-timezone Europe/Moscow 
      cat /vagrant/rundeck_id_rsa > #$rundeck_home/.ssh/id_rsa
      cat /vagrant/rundeck_id_rsa.pub > #$rundeck_home/.ssh/id_rsa.pub
      cat /vagrant/rundeck_ssh_config > #$rundeck_home/.ssh/config
      chown rundeck:rundeck #$rundeck_home/.ssh/config
      chmod 0400 #$rundeck_home/.ssh/config
      cp /vagrant/nodes.yml #$rundeck_home/.
      chown rundeck:rundeck #$rundeck_home/nodes.yml
    SHELL
  end

  config.vm.define "debian" do |debian|
    debian.vm.provision "shell", inline: <<-SHELL
      timedatectl set-timezone Europe/Moscow
      apt update
      apt -y install nfs-common
      mkdir /testdir
      chown vagrant:vagrant /testdir
    SHELL
  end

  config.vm.define "freebsd" do |freebsd|
    freebsd.vm.provision "shell", inline: <<-SHELL
      cp /usr/share/zoneinfo/Europe/Moscow /etc/localtime
      mkdir /nfs_data
      chown vagrant:vagrant /nfs_data
      mkdir /backups
      chown vagrant:vagrant /backups
      cat /vagrant/freebsd_nfs_rc.conf >> /etc/rc.conf
      cp /vagrant/freebsd_exports /etc/exports
      mountd -r
      pkg install -y -q gnu-watch
    SHELL
  end

  MACHINES.each do |boxname, boxconfig|

    config.vm.define boxname do |box|

        box.vm.box = boxconfig[:box_name]
        # Disable Gueast Additions install
        box.vbguest.auto_update = false
        box.vm.host_name = boxname.to_s
        box.vm.network "private_network", ip: boxconfig[:ip_addr]

        box.vm.provider :virtualbox do |vb|
          vb.memory = boxconfig[:memory]
          vb.cpus = boxconfig[:cpus]
        end

        box.vm.provision "shell", inline: <<-SHELL
          cat /vagrant/hosts >> /etc/hosts
          cat /vagrant/rundeck_id_rsa.pub >> /home/vagrant/.ssh/authorized_keys
        SHELL
    end
  end

  config.vm.define "freebsd" do |freebsd|
    freebsd.vm.provision "shell", inline: <<-SHELL
      reboot
    SHELL
  end

end
