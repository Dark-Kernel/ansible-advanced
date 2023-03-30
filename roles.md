# Roles
Roles are the recommended way of developing playbooks in ansible, roles help us to organize our project in a standard structure. we use roles in our project and share with open community through github or ansible-galaxy.

When you create a role it automatically create a folder structure for you that contians a set of folder like tasks, vars, handlers, etc, and readme file is initialzed automatically this will help in creating roles in best practice. Once roles are created it is easy to use in any part of your playbook. Simply use the roles directive to assign the roles to a server and ansible automatically loads all the tasks from `tasks` folder and variable from `vars` folder and default from `defaults` folder and make avaiable for the playbook and you don't have to use any `include` command in the main playbook if you are using roles, this way our main plyabooks looks very very simple and readable and we can use the same roles in different part of the playbooks the same way.


## EXAMPLE

we willl create two simple roles 
1. mysql_db
2. flask_web

use the ansible-galaxy command:
```bash
ansible-galaxy init mysql_db
ansible-galaxy init flask_web
```
alternatively, you can also create the directory structure youself but ansible-galaxy makes it easy for us.

Then create the tasks under `tasks` directory.
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

Now, we will use `roles` directive to use these tasks in our main playbook.

```YAML
# Ansible Playbook

- name: Deploy Web Application
  hosts: db_and_web_server
  roles:
    - mysql_db
    - flask_web
```


