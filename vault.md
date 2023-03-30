# Vault

We have been storing credential used to connect to target server in a plain text format so far, Storing password and credential in plain text format is not best practice.

Ansible vault helps us to store this data in an encrypted format, There are multiple ways to use ansible vault, since we already have an inventory file we will try to encrypt the current inventory file it self.

To do this use the ansible-vault command:
```bash
ansible-vault encrypt inventory.txt
```
When this is run you will be prompted for new vault password, after that file will be encrypted and will become unreadable.

Now, if we will run the playbook, we will get error
```YAML
ERROR: Attempted to read "inventory.txt" as ini file: Decryption failed on inventory.txt
```
If you are using any encrypted file like this in ansible project, then append the option `-ask-vault-pass`:
```bash
ansible-playbook playbook.yml -i inventory.txt -ask-vault-pass
```
Now, after entering the same password we set playbook will be executed.

Instead of prompting for vault password, you could have the vault password stored in a file and have that file passed in as a value to `-vault-password-file` option:
```bash
ansible-playbook playbook.yml -i inventory.txt -vault-password-file ~/.vault_pass.txt
```

But, passing password file in plain text is not secured. 
So we will use third method by passing a python script.
```bash
ansible-playbook playbook.yml -i inventory.txt -vault-password-file ~/.vault_pass.py
```
This will be a script this will get the password by a program. 


To View the content of a encrypted file you can use :
```bash
ansible-vault view inventory.txt
```
To create an encrypted file use:
```bash
ansible-vault create inventory.txt
```
Ansbile Vault is only used in command line not in playbook.

