

# How to Run

## Before Hand
   - create a inventory file, with the hosts and IPs
   - generate a new ssh and install this on any boxes you wish to provision
   - create a admin user on each box called 'superman'
   - on each box ensure OpenSSL server and python are preinstalled. (without there will be no connection or abiltiy to run the ansible modules.)
   - in the vault-password-file.txt file, enter the password for the superman user.
   - create a vault password file with the encrpyted values  ( see Ansible Vault Documentation )
    ```
        ansible-vault encrypt_string 
    ```

## Run Against Vagrant
```
  vagrant up
```

## Run aganist a real box

### Create a vault password file

So to secure the inventory file, we need to create a salt key.

```
echo "MyPassword" > $(project_dir)/vault-password-file.txt 
```
### Create inventory file 

```
all:
    hosts:
        developer.machine.com:
          ansible_host: <IP or Domain>
          ansible_user: superman
          ansible_become_pass: <superman password>
          ansible_ssh_pass:    <superman password>
```

save the file.

For security encrypt the file with ansible vault

```
ansible-vault encrypt ./provision/inventory.yml --vault-password-file=vault-password-file.txt 
```

Now we need to edit the ubuntuDevMachine.yml.
So to customise the build, we need to specify the developers account name and password.
change the following lines:

```
   system_user: danfoulkes
   system_password: "$6$AXe355Juu21$zLqAAHyhL41xQhGCa4XRHDUPhl5NabKaygeAe0NCIWY9ypfjsd7AC/hdVD6HRATcUzBEJhU/5NZmuJTX1glE1."
```

*IMPORTANT !!!!*
the encryption used for the password was not the one used by vault, instead you can generate this hash with the folllowing command:
for altantive options see: [ how do i generate crypted passwords for the user module documentation.](https://docs.ansible.com/ansible/latest/reference_appendices/faq.html#how-do-i-generate-crypted-passwords-for-the-user-module)


For copy and pasting, this worked for me:
```
ansible all -i localhost, -m debug -a "msg={{ 'mypassword' | password_hash('sha512', 'mysecretsalt') }}"
```


```
  ansible-playbook --inventory-file=inventory.yml -u superman -k ubuntuDevMachine.yml --key-file=dan_dev_box_rsa --vault-password-file=../vault-password-file.txt --ask-pass
```