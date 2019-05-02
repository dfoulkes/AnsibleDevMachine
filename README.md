

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
```
  ansible-playbook --inventory-file=inventory.yml -u superman -k ubuntuDevMachine.yml --key-file=dan_dev_box_rsa --vault-password-file=../vault-password-file.txt -vvv --ask-pass
```