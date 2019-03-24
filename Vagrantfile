# -*- mode: ruby -*-
# vi: set ft=ruby :

$provision_script = <<PROVISION
    apt-get -y update
    apt-get install ifupdown -y
PROVISION

def gui_enabled?
    !ENV.fetch('GUI','').empty?
end

Vagrant.configure("2") do |config|
	config.vm.provision "shell", inline: $provision_script
	config.vm.define "ctf-ubuntu" do |ubuntu|
	
	ubuntu.vm.box = "catdevman/ubuntu-18.04-desktop-amd64-virtualbox"
	ubuntu.vm.hostname = "invalid-ctf"
	ubuntu.ssh.username = 'vagrant'
	ubuntu.ssh.password = 'vagrant'
	ubuntu.ssh.forward_agent = true

	ubuntu.vm.network "private_network", type: "dhcp"

    #config.vm.provision "shell" do |s|
    #    ssh_insecure_key = File.readlines("#{Dir.home}/.vagrant.d/insecure_private_key").first.strip
    #    s.inline = <<-SHELL
    #        echo #{ssh_insecure_key} >> /home/vagrant/.ssh/id_rsa
    #        chown vagrant /home/vagrant/.ssh/id_rsa
    #        chmod 400 /home/vagrant/.ssh/id_rsa
    #    SHELL
    #end

    ubuntu.vm.synced_folder ".", "/vagrant", type: "rsync", disabled: false, rsync__verbose: false
    ubuntu.vm.synced_folder "host-share", "/media/host-share", type: "rsync", disabled: false, rsync__verbose: false

    ubuntu.vm.provision "ansible_local" do |ansible|
		ansible.playbook = "playbook.yml"
		#ansible.galaxy_role_file = "requirements.yml"
	end

	ubuntu.vm.provider "virtualbox" do |vb|
        vb.gui = gui_enabled?
		vb.customize ["modifyvm", :id, "--memory", "4096"]
		vb.customize ["modifyvm", :id, "--cpus", "2"]
		vb.customize ["modifyvm", :id, "--vram", "256"]
	end

        config.vm.network 'private_network', ip: '192.168.56.101', auto_config: false
	end
end

