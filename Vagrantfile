# Vagrant configuration version
Vagrant.configure("2") do |config|

# Hostname of VM
config.vm.hostname = "server"
# Box to be installed in VM
config.vm.box = "ubuntu/focal64"

# Network configuration
config.vm.network :private_network, ip: "192.168.0.17"
config.vm.network "forwarded_port", guest:80, host: 8080, auto_correct: true

# Provider to be used for VM
config.vm.provider :virtualbox do |vb|
	# VM name
	vb.name = "Moodle_403"
	# VM memory (1 GB)
	vb.memory = "1024"
	# VM cpu
	vb.cpus = 1
end

config.vm.synced_folder ".", "/vagrant"

end
