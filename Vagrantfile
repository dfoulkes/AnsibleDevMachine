Vagrant.require_version ">= 1.7.0"


Vagrant.configure(2) do |config|
    config.vm.box = "ubuntu/bionic64"
  
    config.vm.provider "virtualbox" do |v|
      v.memory = 6144
      v.cpus = 4
      v.gui = true
      v.customize ["modifyvm", :id, "--vram", "128"]
    end

    config.vm.provision :shell, inline: "echo 'GoingBack2505!' > /tmp/vault_pass"

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
    