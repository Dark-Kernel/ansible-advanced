# Error Handling

In single sever playbook, when ansible fails in any particular step it exits the playbook. 


What if there are multiple servers involved.?
```YAML
# Ansible Playbook
- 
  name: Deploy Web Application
  hosts: server1, server2, server3
  tasks:
    - name: Install dependencies

    - name: Install Mysql
    
    - name: Start Mysql Service
  
    - name: Install Flask dependencies

    - name: Run web-server
```
In this case playbook is running against 3 servers.
when we start playbook it will first install dependencies on all servers, then installs Mysql server, But in third task one server fails, so what happen is ansbile takes that server out of the list and continues to execute the remainder tasks across the availabel and healthy servers, ansible will make an attempt to complete as many servers possible. This is the deafault behaviour.

In case, we would like it to be consistent and want ansible to stop the execution of playbook if one of the server fails, add an option called `any_errors_fatal` and to true in play. This way if any task fail on one server ansible stop the execution of the play alltogether and exits.

```YAML
# Ansible Playbook
- 
  name: Deploy Web Application
  hosts: server1, server2, server3
  any_errors_fatal: true
  tasks:
    - name: Install dependencies

    - name: Install Mysql
    
    - name: Start Mysql Service
  
    - name: Install Flask dependencies

    - name: Run web-server
```

###### 
#### Ignore errors.

At the end of our we Application playbook we would like to a new task to send out a notification email that web server is ready, we use the `mail` module to send an email to our team with the subject and body.

```YAML
  - mail: 
      to: devops@corp.com
      subject: Server Deployed!
      body: Web Server Deployed
```
However, this is not a critical requirement and our smtp server is not very stable and we really don;t care it this task completes successfully or not, even if the mail don't go through a time, we are still ok and don't want the playbook to fail because of it. 

To simple ignore errors we could simple set `ignore_errors` to `yes` on the tasks, so the execution of playbook will not stop and will ignore the error and continues.

```YAML
  - mail: 
      to: devops@corp.com
      subject: Server Deployed!
      body: Web Server Deployed
    ignore_errors: yes
```

 
#### Error checking 

We would like to do some error checking, Example we would like to check the content of the file called server.log, and fail the playbook if it has errors in it.

```YAML
  - mail: 
      to: devops@corp.com
      subject: Server Deployed!
      body: Web Server Deployed
    ignore_errors: yes

  - command: cat /var/log/server.log
    register: command_output
    failed_when: "'ERROR' in command_output.stdout"
```

We are going to use `failed_when` directive, we will write a condition in which it will look for the word 'ERROR' in the stdout and if exists ansible will fail the play, because the value in failed_when will become true. 



