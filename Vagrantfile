# -*- mode: ruby -*-
# vi: set ft=ruby :

vmname = "dc"
hostname = "dc"
domain_fqdn = "bitfrickler.local"
domain_netbios = "BITFRICKLER"
domain_safemode_password = "Vagrant123#"

Vagrant.configure("2") do |config|
  config.vm.define "dc" do |cfg|
    cfg.vm.box = "windows_server_2016_dc_core"
    cfg.vm.hostname = hostname

    # use the plaintext WinRM transport and force it to use basic authentication.
    # NB this is needed because the default negotiate transport stops working
    #    after the domain controller is installed.
    #    see https://groups.google.com/forum/#!topic/vagrant-up/sZantuCM0q4
    cfg.winrm.transport = :plaintext
    cfg.winrm.basic_auth_only = true

    cfg.vm.communicator = "winrm"
    cfg.vm.network :forwarded_port, guest: 5985, host: 5985, id: "winrm", auto_correct: true
    cfg.vm.network :forwarded_port, guest: 22, host: 2222, id: "ssh", auto_correct: true
    cfg.vm.network :forwarded_port, guest: 3389, host: 3389, id: "rdp", auto_correct: true
    cfg.vm.network :private_network, ip: "10.3.1.2"
    
    cfg.vm.provision "shell", path: "vagrant-active-directory-2016/scripts/remove_defender.ps1", privileged: false
    cfg.vm.provision "shell", path: "vagrant-active-directory-2016/scripts/disable_wu.ps1", privileged: false
    cfg.vm.provision "shell", path: "vagrant-active-directory-2016/scripts/disable_rdp_nla.ps1", privileged: false
    cfg.vm.provision "shell", path: "vagrant-active-directory-2016/scripts/install_ad.ps1", privileged: false 
    cfg.vm.provision "reload"
    cfg.vm.provision "shell", path: "vagrant-active-directory-2016/scripts/configure_ad.ps1", privileged: false, args: "'#{domain_fqdn}' '#{domain_netbios}' '#{domain_safemode_password}'"
    cfg.vm.provision "reload"

    cfg.vm.provider "virtualbox" do |vb, override|
      vb.name = vmname
      vb.gui = false
      vb.customize ["modifyvm", :id, "--memory", 1024]
      vb.customize ["modifyvm", :id, "--cpus", 1]
      vb.customize ["modifyvm", :id, "--vram", "16"]
      vb.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
      vb.customize ["setextradata", "global", "GUI/SuppressMessages", "all" ]
      vb.customize ["modifyvm", :id, "--macaddress1", "auto"]
      vb.customize ["modifyvm", :id, "--macaddress2", "auto"]
    end
  end
end