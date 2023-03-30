# Prequisites
This is an advance course, so there are some prequisites:
* YAML
* Ansible Basics
* Inventory Files
* Playbooks
* Modules

we will do some complex tasks in this tutorial. we will be learning more Playbooks to develop some reallife use cases.

# Covered Topics

* Roles
* Asynchronous Actions
* Error Handling
* Jinja 2 Templating
* Lookups
* Vault
* Dynamic Inventory
* Custom Modules
* Plugins

# Environment:

We can use : 
* VMs
* Docker
* vagrant

######
#### Docker setup

Docker makes these things easy for us, and it is recommended.
Make sure docker is installed in your system.

we will create multiple containers, with `ssh` enabled.


1. Pull the below image:
```bash
docker pull darkkernel/ubuntu-ssh
```
2. Create 3 containers of above image: 
```bash
docker run -it -d --name target1 ubuntu-ssh
docker run -it -d --name target2 ubuntu-ssh
docker run -it -d --name target3 ubuntu-ssh
```

> Remember to set `host_key_checking` to `False`. 

3. Now, get the IP's of containers to create your inventory file:
```bash
docker inspect target1 target2 target3 | grep IPAddress
```

Inventory file `inventory.txt` :

```txt
target1 ansible_host=172.17.0.2 ansible_ssh_pass=pass ansible_python_interpreter=/usr/bin/python3
target2 ansible_host=172.17.0.3 ansible_ssh_pass=pass ansible_python_interpreter=/usr/bin/python3
target3 ansible_host=172.17.0.4 ansible_ssh_pass=pass ansible_python_interpreter=/usr/bin/python3
```

4. Quick connection test:

```bash
ansible -m ping all -i inventory.txt
```

Output must be:
```
target1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
target3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
target2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```


So, our environment is ready and we are good to go.


