Vagrant.require_version ">= 1.7.0"

$install_ansible = <<SCRIPT
sudo apt-get update -y
sduo reboot
SCRIPT


$install_ansible = <<SCRIPT
sudo apt-get update -y
sudo apt-get install software-properties-common -y
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update -y
sudo apt-get install ansible -y
SCRIPT

$set_permissions_to_owner_only = <<SCRIPT
sudo chown vagrant /tmp/vault_pass
sudo chmod 640 /tmp/vault_pass
SCRIPT
Vagrant.configure(2) do |config|
    config.vm.box = "bento/ubuntu-16.04"
  
    config.vm.provider "hyperv" do |v|
      v.maxmemory = 16000
      v.memory = 6144
      v.cpus = 4
      v.enable_virtualization_extensions = true
      v.auto_stop_action = "ShutDown"
      v.auto_start_action = "Start"
    end
  # REMEBER!!! ADD PASSWORD FILE FOR THIS TO WORK.
    config.vm.provision "file", source: "./vault_pass", destination: "/tmp/vault_pass"
    config.vm.provision "shell", inline: $set_permissions_to_owner_only, run: "always"
   #too tired, tomrrow recall what this variable should be
    config.vm.provision "file", source: "./vault.yml", destination: ".gitconfig"
    config.vm.provision "file", source: "./provision", destination: "/home/vagrant/"
    config.vm.provision "shell", inline: $install_ansible, run: "always"
    config.vm.define "devmachine" do |devmachine|
        devmachine.vm.provision 'preemptively give others write access to /etc/ansible/roles', type: :shell, inline: <<~'EOM'
        mkdir /etc/ansible/roles -p
        chmod o+w /etc/ansible/roles
        EOM

        devmachine.vm.provision "ansible_local" do |ansible|
            ansible.verbose = true
            ansible.playbook = "provision/ubuntuDevMachine.yml" 
            ansible.galaxy_role_file = 'provision/requirements.yml' 
            ansible.galaxy_roles_path = '/etc/ansible/roles'
            ansible.vault_password_file = "/tmp/vault_pass"
        end     
    end    

end
    