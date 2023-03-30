# Asynchronous Actions
As we discussed before ansible eastablishes connectivity to target over ssh, this means ssh keep alive through out the execution of task.

**Cases:**

* At time we may want to execute a long running tasks or a proecess that exceeds the ssh timeout
* we may simpley don't want ssh to keep alive throught out the operation instead invoke a proecess and check on later time
* we may want to run multiple processes at once and check on them later.
* And another case we may want to run multiple tasks and don't bother about it.

All these can be achieved using Asynchronous Actions.

Let's say as a part of our webserver deployment we have script that monitors and perform health checks on webserver 
```YAML
# Ansible Playbook
- 
  name: Deploy Web Application
  hosts: db_and_web_server
  tasks:
    - command: /opt/monitor_webapp.py
```

The script perform various monitoring and health check including a stability check to ensure that webserver stays online at least 5 mins without any issue.

This script takes at least 5 mins to complete execution, but we don't want ansible to hold the ssh session alive until the script finishes the execution. We want to tell ansible that this is an Asynchronous task so kick it off and check on it after a 1 min. 

So we use `async` directive to specify the maxinmum time we woould expect the task to execute, in our case lets say 6mins, considering other checks which needs to be performed. 
`async` directive tells ansible that this is a Asynchronous task to kick it off and check on it later. And how frequently does ansible check the status of the script By default ansible check on every 10secs if you want to change that you can use `poll` directive we don't want to check on 10 s and we don't want to wait for 5 mins because what if the script fails after 1min ,we don't want to waste the remaining 4 mins. So checking every 1min seems good. so we set the `poll` value to 60. 

```YAML
# Ansible Playbook
- 
  name: Deploy Web Application
  hosts: db_and_web_server
  tasks:
    - command: /opt/monitor_webapp.py
      async: 360
      poll: 60
```

We will add another task for the database.
```YAML
# Ansible Playbook
- 
  name: Deploy Web Application
  hosts: db_and_web_server
  tasks:
    - command: /opt/monitor_webapp.py
      async: 360
      poll: 60

    - command: /opt/monitor_database.py
      async: 360
      poll: 60
```
And we will give it 6 mins to execute and poll every 60s just like previous task.

What happens here? 

when ansible starts execution it first executes the first task but it is still going to wait for that task to finish, Asynchronous does not mean ansible is going to run that going ot move forward. It is goinng to monitor the status of the scirpt and wait until the time speciefied is elaspsed,  in this case 360sec , In this case it is goping to run first check and wait for 6min to finish then run the second check to monitor the databse and 6 min for that. 

What we could which saves lot of time by running both of them in parallel, so we could like to run first task and immediatedly move to second task and kick off the second check and wait for  at the end, we can do by setting poll value to 0 , by setting it we are asking ansible to not to wait for there immediatedly go to next task.


```YAML
# Ansible Playbook
- 
  name: Deploy Web Application
  hosts: db_and_web_server
  tasks:
    - command: /opt/monitor_webapp.py
      async: 360
      poll: 0

    - command: /opt/monitor_database.py
      async: 360
      poll: 0
```

However after kicking off the second task it is going to exited we havem;t done anyting to makes ansile wait for the task to finish at the end, for that we must first register the result of that task to a variable.

```YAML
# Ansible Playbook
- 
  name: Deploy Web Application
  hosts: db_and_web_server
  tasks:
    - command: /opt/monitor_webapp.py
      async: 360
      poll: 0
      register: webapp_result

    - command: /opt/monitor_database.py
      async: 360
      poll: 0
      register: database_result

    - name: Check status of tasks
      async_status: jid={{ webapp_result.ansible_job_id }}
      register: job_result
      until: job_result.finished
      retries: 30
```
Now we will have a new task to check the status of task, and we use `async_status` module to check the status of the task, one of the parameter `async_status` takes is `jobid` and we can get the job id of the previous task using `webapp_result.ansible_job_id`. And we are going to wait until that task is finished. 

> All modules do not support async, so make sure that whatever module you are using supports async.





