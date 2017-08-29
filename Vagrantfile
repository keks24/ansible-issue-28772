# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.box = "debian/stretch64"

    ## when using non "contrib-" boxes
    ## do NOT use rsync: https://stackoverflow.com/a/34882657/1961102
    ## and use special mount-options, otherwise SSH fails due to unprotected SSH private key: https://stackoverflow.com/a/34485973/1961102
    config.vm.synced_folder ".", "/vagrant", type: "virtualbox", owner: "vagrant", mount_options: ["dmode=775,fmode=600"]

    # disable auto_update on "debian/contrib-jessie64" box, as it is not working
    if Vagrant.has_plugin?("vagrant-vbguest")
        config.vbguest.auto_update = true
        # never download from online, always use local installed one
        config.vbguest.no_remote = true
    end

    config.vm.box_check_update = true

    # internal VM which is configured like the real machine
    config.vm.define "development-vm" do |vmConfig|
        # do NOT have shared vagrant-folder present (as this requires rsync or guest-additions to be installed)
        vmConfig.vm.synced_folder ".", "/vagrant", disabled: true
        # do NOT install guest-additions
        vmConfig.vbguest.auto_update = false

        vmConfig.vm.network "private_network", ip: "192.168.100.100", virtualbox__intnet: "development_network"
        vmConfig.vm.network "forwarded_port", guest: 80, host: 24242

        vmConfig.vm.provider "virtualbox" do |vb|
            vb.memory = "4096"
            vb.cpus = 2
            vb.name = "development-vm"

            # make it small ;)
            vb.linked_clone = true
        end
    end

    # internal VM to deploy from
    config.vm.define "provision-vm" do |vmConfig|
        config.vm.hostname = "provision-vm"
        vmConfig.vm.network "private_network", ip: "192.168.100.99", virtualbox__intnet: "development_network"

        vmConfig.vm.provider "virtualbox" do |vb|
            # we dont need much for this VM
            vb.memory = "512"
            vb.cpus = 1
            vb.name = "provision-vm"

            # make it small ;)
            vb.linked_clone = true
        end

        # as I'm developing on Windows, there is no local ansible, so use ansible (via PIP) inside the VM
        vmConfig.vm.provision "ansible_local" do |ansible|
            ansible.playbook = "provisioning/create_structure.yml"
            # special inventory file, since we have two VMs here
            ansible.inventory_path = "provisioning/inventory"
            # required for disabling "unknown hosts"-check
            ansible.config_file = "provisioning/ansible.cfg"
            ansible.verbose = false
            ansible.limit = "development-vm"
            ansible.install_mode = :pip
        end

        if ENV['playbook'] == "deploy"
            vmConfig.vm.provision "ansible_local" do |ansible|
                ansible.playbook = "provisioning/deploy_files.yml"
                # special inventory file, since we have two VMs here
                ansible.inventory_path = "provisioning/inventory"
                # required for disabling "unknown hosts"-check
                ansible.config_file = "provisioning/ansible.cfg"
                ansible.verbose = false
                ansible.limit = "development-vm"
                ansible.install_mode = :pip
            end
        end
    end
end
