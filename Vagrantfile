# -*- mode: ruby -*-
# vi: set ft=ruby :

server_ip = "192.168.42.110"

agents = { "wilSW" => "192.168.42.111"}

# Extra parameters in INSTALL_K3S_EXEC variable because of
# K3s picking up the wrong interface when starting server and agent
# https://github.com/alexellis/k3sup/issues/306

server_script = <<-SHELL
	sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-Linux-*
	sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-Linux-*
	sudo curl -sfL https://get.k3s.io | sh -
	sleep 5
	sudo dnf install epel-release
	sudo ln -sf /opt/VBoxGuestAdditions-5.1.20/lib/VBoxGuestAdditions/mount.vboxsf /sbin/mount.vboxs
	sudo -E env "PATH=$PATH" cp /var/lib/rancher/k3s/server/token /vagrant_shared
	sudo -E env "PATH=$PATH" cp /etc/rancher/k3s/k3s.yaml /vagrant_shared
	SHELL

agent_script = <<-SHELL
	sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-Linux-*
	sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-Linux-*
	sudo dnf install epel-release
	sudo ln -sf /opt/VBoxGuestAdditions-5.1.20/lib/VBoxGuestAdditions/mount.vboxsf /sbin/mount.vboxs
	export K3S_TOKEN_FILE=/vagrant_shared/token
    	export K3S_URL=https://#{server_ip}:6443
    	export INSTALL_K3S_EXEC="--flannel-iface=eth1"
	sudo curl -sfL https://get.k3s.io | sh -
	SHELL

Vagrant.configure("2") do |config|
	config.vm.box = "centos/8"

	config.vm.define "wilS", primary: true do |server|
		server.vm.network "public_network", bridge: 'en1: Thunderbolt 1', ip: server_ip
		server.vm.synced_folder "./shared", "/vagrant_shared"
		server.vm.hostname = "wilS"
		server.vm.provider "virtualbox" do |vb|
			vb.gui = false
			vb.customize ["modifyvm", :id, "--name", "wilS"]
			vb.memory = "1024"
			vb.cpus = "1"
		end
		server.vm.provision "shell", inline: server_script
	end

	agents.each do |agent_name, agent_ip|
		config.vm.define agent_name do |agent|
			agent.vm.network "public_network", bridge: 'en1: Thunderbolt 1', ip: agent_ip
			agent.vm.synced_folder "./shared", "/vagrant_shared"
			agent.vm.hostname = agent_name
			agent.vm.provider "virtualbox" do |vb|
				vb.gui = false
				vb.customize ["modifyvm", :id, "--name", "wilSW"]
				vb.memory = "1024"
				vb.cpus = "1"
			end
			agent.vm.provision "shell", inline: agent_script
		end
	end
end
