# File Separation

So far we are being defining variables in the same inventory file, however it is not the best practice, better to define this in seperate file for that server.

First create `host_vars` dir next to the playbook and create a `yaml` file with same name as of the server.
Example:

```txt
# Inventory file

db_server ansible_host=192.168.0.3 ansible_ssh_pass=password
```
so create a file named `db_server.yml`:
```YAML
ansible_host: 192.168.0.3
ansible_ssh_pass: password 
```
You don't need any further configuration, when ansible playbook is executed ansible automatically reads the values from this file and associates them with the host.

so it's important to name the file with same name as of the server, and also it's important to name the folder `host_vars` because that's the folder ansible look to find out whether there is variable for a particular server.

Host folders must me named `host_vars` && group folder must be named as `group_vars`.

---

## INCLUDE statement.

Example: 

we have a playbook with two sets of tasks:

1. Setup Database
2. Setup Webserver

What if we need to reuse these tasks in different playbooks where their requirement may be to only install the **database** or **webserver**

So we decide to divide these tasks in two sets, create two files under folder `tasks` naming them `deploy_db.yaml` and `deploy_web.yaml`:

```YAML
# tasks/deploy_db.yaml

- name: Install Dependencies
- name: Install Mysql
- name: Start Mysql service
- name: Create Application Database
- name: Create Application DB User
```
```YAML
# tasks/deploy_web.yaml

- name: Install Python Flask Dependencies
- name: Copy web-server codes
- name: Run the web-server
```

Now, we can use these tasks in our playbook using `include` statement.

```YAML
# Ansible Playbook

- name: Deploy Web Application
  hosts: db_and_web_server
  tasks:

  - include: tasks/deploy_db.yaml
  - include: tasks/deploy_web.yaml
```



