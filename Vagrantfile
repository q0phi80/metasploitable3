# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.synced_folder '.', '/vagrant', disabled: true
  config.vm.define "ub1404" do |ub1404|
    ub1404.vm.box = "rapid7/metasploitable3-ub1404"
    ub1404.vm.hostname = "meta"
    config.ssh.username = 'vagrant'
    config.ssh.password = 'vagrant'

    ub1404.vm.network "private_network", ip: '172.28.128.3'

    ub1404.vm.provider "virtualbox" do |v|
      v.name = "Meta"
      v.memory = 1024
    end
  end

config.vm.define "Kali" do |kali|
    kali.vm.box = "kalilinux/rolling"
    kali.vm.hostname = "Kali"
    kali.vm.disk :disk, size: "50B", primary: true
    
    kali.vm.network "private_network", ip: '172.28.128.100'
    
    kali.vm.provider "virtualbox" do |v|
      v.name = "Kali"
      v.memory = 2048
      v.cpus = 2
    end
  end

config.vm.define "win10" do |win10|
		#win10.gui = true
		win10.vm.box = "gusztavvargadr/windows-10"
		win10.vm.hostname = "WKSTN01"
		config.winrm.timeout =   1800 # 30 minutes
		config.vm.boot_timeout = 1800 # 30 minutes
		#win10.winrm.retry_limit = 600
		#win10.winrm.retry_delay = 10
		
		win10.vm.disk :disk, size: "40B", primary: true
		
		win10.vm.network "private_network", ip: '172.28.128.11'
    
	win10.vm.provider "virtualbox" do |v|
		v.name = "Win10"
		v.memory = 4096
    end
  end

config.vm.define "win2k8" do |win2k8|
    # Base configuration for the VM and provisioner
    win2k8.vm.box = "rapid7/metasploitable3-win2k8"
    win2k8.vm.hostname = "DC01"
    win2k8.vm.communicator = "winrm"
    win2k8.winrm.retry_limit = 60
    win2k8.winrm.retry_delay = 10

    win2k8.vm.network "private_network", ip: '172.28.128.10'
  
    win2k8.vm.provider "virtualbox" do |v|
      v.name = "Win2k8"
    end

    win2k8.vm.provider "libvirt" do |v|
      v.memory = 2048
      v.cpus = 2
      v.video_type = 'qxl'
      v.input :type => "tablet", :bus => "usb"
      v.channel :type => 'unix', :target_name => 'org.qemu.guest_agent.0', :target_type => 'virtio'
      v.channel :type => 'spicevmc', :target_name => 'com.redhat.spice.0', :target_type => 'virtio'
      v.graphics_type = "spice"

      # Enable Hyper-V enlightenments: https://blog.wikichoon.com/2014/07/enabling-hyper-v-enlightenments-with-kvm.html
      v.hyperv_feature :name => 'stimer',  :state => 'on'
      v.hyperv_feature :name => 'relaxed', :state => 'on'
      v.hyperv_feature :name => 'vapic',   :state => 'on'
      v.hyperv_feature :name => 'synic',   :state => 'on'
    end

    # Configure Firewall to open up vulnerable services
    case ENV['MS3_DIFFICULTY']
      when 'easy'
        win2k8.vm.provision :shell, inline: "C:\\startup\\disable_firewall.bat"
      else
        win2k8.vm.provision :shell, inline: "C:\\startup\\enable_firewall.bat"
        win2k8.vm.provision :shell, inline: "C:\\startup\\configure_firewall.bat"
    end

    # Insecure share from the Linux machine
    win2k8.vm.provision :shell, inline: "C:\\startup\\install_share_autorun.bat"
    win2k8.vm.provision :shell, inline: "C:\\startup\\setup_linux_share.bat"
    win2k8.vm.provision :shell, inline: "rm C:\\startup\\*" # Cleanup startup scripts
  end
end
