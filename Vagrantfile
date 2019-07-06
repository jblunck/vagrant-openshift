Vagrant.configure("2") do |config|
	config.vm.box = "centos/7"

	config.vm.network "forwarded_port", guest: 8443, host: 8443

	# too many levels of symlinks therefore we use --links
	config.vm.synced_folder ".", "/vagrant", type: "rsync",
	    rsync__args: ["--verbose", "--archive", "--delete", "-z", "--links"]

	config.vm.provider :libvirt do |libvirt|
		libvirt.uri = "qemu:///system"
		libvirt.cpus = 4
		libvirt.memory = 4096
	end

	config.vm.provision :ansible_local do |ansible|
		ansible.install_mode    = "pip"
		# the following requires vagrant 2.2.5
		ansible.pip_install_cmd = "sudo rpm -ivh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm ; sudo yum -y --enablerepo=epel install python-pip"
		ansible.version         = "2.7.11"
		ansible.inventory_path  = "hosts"
		ansible.limit           = "all" # or only "nodes" group, etc.
		ansible.playbook        = "docker-storage.yml"
		ansible.verbose         = "vvv"
		ansible.become          = true
	end

	config.vm.provision :ansible_local do |ansible|
		ansible.version         = "2.7.11"
		ansible.inventory_path  = "hosts"
		ansible.limit           = "all" # or only "nodes" group, etc.
		ansible.playbook        = "roles/openshift-ansible/playbooks/prerequisites.yml"
		ansible.verbose         = "vvv"
		ansible.become          = true
	end

	config.vm.provision :ansible_local do |ansible|
		ansible.version         = "2.7.11"
		ansible.inventory_path  = "hosts"
		ansible.limit           = "all" # or only "nodes" group, etc.
		ansible.playbook        = "roles/openshift-ansible/playbooks/deploy_cluster.yml"
		ansible.verbose         = "vvv"
		ansible.become          = true
	end

end
