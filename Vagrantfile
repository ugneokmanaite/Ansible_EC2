
Vagrant.configure("2") do |config|
	# Web Virtual Machine
	config.vm.define "web" do |web|
		web.vm.box = "ubuntu/xenial64"
		web.vm.network :private_network, ip: "192.168.33.10"
		web.vm.hostname = "web"
		# Provisioning with vagrant is faster for local environment
		web.vm.synced_folder "app", "/home/ubuntu/app"
		web.hostsupdater.aliases = ["development.web"]
	end
	# DB Virtual Machine
	config.vm.define "db" do |db|
		db.vm.box = "ubuntu/xenial64"
		db.vm.network :private_network, ip: "192.168.33.11"
		db.vm.hostname = "db"
		# db.hostsupdater.aliases = ["development.db"]
	end
#	AWS Virtual Machine
	config.vm.define "aws" do |aws|
		aws.vm.box = "ubuntu/xenial64"
		aws.vm.network :private_network, ip: "192.168.33.12"
		aws.vm.hostname = "aws"
		aws.hostsupdater.aliases = ["development.aws"]
	end
#	Ansible Virtual Machine
	config.vm.define "ansible" do |ansible|
		ansible.vm.box = "ubuntu/xenial64"
                ansible.vm.provision "shell", path: "ansible-provision.sh"
		ansible.vm.synced_folder "ansible", "/home/ubuntu/ansible"
		ansible.vm.synced_folder "app", "/home/ubuntu/app"
		ansible.vm.network :private_network, ip: "192.168.33.13"
		ansible.vm.hostname = "ansible"
		ansible.hostsupdater.aliases = ["development.ansible"]
	end
end
