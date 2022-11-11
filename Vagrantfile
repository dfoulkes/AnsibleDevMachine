Vagrant.require_version ">= 1.7.0"

$install_ansible = <<SCRIPT
sudo apt-get update -y
sduo reboot
SCRIPT


$install_ansible = <<SCRIPT
export HOME=/home/vagrant
sudo apt-get update -y
sudo apt-get install software-properties-common -y
sudo apt-get install python3-pip -y

sudo pip3 install ansible
# sudo apt-add-repository ppa:ansible/ansible
# sudo apt-get update -y
# sudo apt-get install ansible -y
SCRIPT

$set_permissions_to_owner_only = <<SCRIPT
sudo chown vagrant /tmp/vault_pass
sudo chmod 640 /tmp/vault_pass
sudo mkdir /vagrant 
sudo mkdir -p /vagrant/ansible/roles
sudo chown vagrant:vagrant -R /vagrant

SCRIPT
Vagrant.configure(2) do |config|
    config.vm.box = "roboxes/ubuntu2210"
    config.vm.box_version = "4.2.0"
    config.vm.provider "hyperv" do |v|
      v.maxmemory = 16384
      v.memory = 6144
      v.cpus = 8
      v.enable_virtualization_extensions = true
      v.auto_stop_action = "ShutDown"
      v.auto_start_action = "Start"
    end
  # REMEBER!!! ADD PASSWORD FILE FOR THIS TO WORK.
    config.vm.provision "file", source: "./vault_pass", destination: "/tmp/vault_pass"
    config.vm.provision "shell", inline: $set_permissions_to_owner_only, run: "always"
   #too tired, tomrrow recall what this variable should be
   # config.vm.provision "file", source: "./vault.yml", destination: ".gitconfig"
    config.vm.provision "file", source: "./provision", destination: "/vagrant/provision"
    config.vm.provision "shell", inline: $install_ansible, run: "always"
    config.vm.define "devmachine" do |devmachine|
        # devmachine.vm.provision 'preemptively give others write access to /etc/ansible/roles', type: :shell, inline: <<~'EOM'
        # mkdir /etc/ansible/roles -p
        # chmod o+w /etc/ansible/roles
        # EOM

        devmachine.vm.provision "ansible_local" do |ansible|
            ansible.verbose = true
            ansible.playbook = "/vagrant/provision/ubuntuDevMachine.yml" 
            ansible.galaxy_role_file = '/vagrant/provision/requirements.yml' 
            ansible.galaxy_roles_path = '/vagrant/ansible/roles'
            ansible.vault_password_file = "/tmp/vault_pass" 
        end     
    end    

end
    