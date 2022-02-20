Vagrant.configure(2) do |config|
	config.vm.box = "centos/8"
	
	config.vm.define "wilS" do |control|
		control.vm.hostname = "wilS"
		control.vm.network "public_network", bridge: 'en1: Thunderbolt 1', ip: "192.168.42.110"
		control.vm.provider "virtualbox" do |v|
			v.customize ["modifyvm", :id, "--name", "wilS"]
			v.memory = 1024
    			v.cpus = 1
		end
		control.vm.provision "k3s shell script", type: "shell",
    			path: "k3s.sh"
	end
	config.vm.define "wilSW" do |control|
		control.vm.hostname = "wilSW"
		control.vm.network "public_network", bridge: 'en1: Thunderbolt 1', ip: "192.168.42.111"
		control.vm.provider "virtualbox" do |v|
			v.customize ["modifyvm", :id, "--name", "wilSW"]
			v.memory = 1024
			v.cpus = 1
		end
		control.vm.provision "k3s shell script", type: "shell",
                        path: "k3s.sh"
	end
end
