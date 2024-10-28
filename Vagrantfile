# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Use the Ubuntu 20.04 box by geerlingguy
  config.vm.box = "geerlingguy/ubuntu2004"

  # Set up network configurations to access the app in a browser
  config.vm.network "forwarded_port", guest: 80, host: 8080  # Frontend
  config.vm.network "forwarded_port", guest: 5000, host: 1234 # Backend
  config.vm.network "forwarded_port", guest: 27017, host: 27017  # MongoDB 

  # Sync the project directory to access code within the VM
  config.vm.synced_folder ".", "/vagrant"

  # Provision with shell to install Ansible and Docker
  config.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
    sudo apt-get install -y ansible docker.io
    sudo usermod -aG docker vagrant
  SHELL

  # Use Ansible provisioner to run the main playbook
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "main.yml"  # Specify the main Ansible playbook
    ansible.inventory_path = "inventory"  # Specify the inventory file if needed
    ansible.extra_vars = {
      ansible_python_interpreter: "/usr/bin/python3"
    }
  end

  # Optional: Restart the VM to apply Docker group changes if needed
  config.vm.provision "shell", inline: "sudo reboot", run: "always"
end
