# Strategy

Strategy defines how a playbook is executed in ansible.

Example, below it is a simple playbook which Deploys web application on single server. And it is pretty straight forward since it is a single server. When ansible starts execution it completes tasks one after another.

```YAML
# Ansible Playbook
- 
  name: Deploy Web Application
  hosts: server1
  tasks:
    - name: Install dependencies

    - name: Install Mysql
    
    - name: Start Mysql Service
  
    - name: Install Flask dependencies

    - name: Run web-server
```
###### 
#### Linear strategy

Now, lets see how it works when multiple servers are involved.

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
In this playbook we are going run the ansible playbook on three different servers, when ansible runs it executes each task across all server in parallel at the same time, it waits for the tasks to finish on all server before proceding to next task. In this case ansible install dependencies across all servers and wait for it to complete on all server before proceding to next task.

So this is called **linear strategy** and this is the default strategy for ansible.


#### Free Strategy 
Another strategy that is available with ansible called free, To enable new strategy is `strategy` directive and specfiy the strategy name.

```YAML
# Ansible Playbook
- 
  name: Deploy Web Application
  strategy: free
  hosts: server1, server2, server3
  tasks:
    - name: Install dependencies

    - name: Install Mysql
    
    - name: Start Mysql Service
  
    - name: Install Flask dependencies

    - name: Run web-server
```

In this case, each server runs all of its tasks independents of other server and do not wait for the task to complete on another servers. So each server can go right to the end as fast it can without bothering about any of the servers.


#### Batch Strategy

There is also a third option, Let's say we have 5 servers but we want ansible to execute three at a time, this is where batch processing helps us. This is not a seperate strategy this is based on the linear strategy but you can control the number of server executed at once or in a batch. 

In the playbook we do not a strategy option so it uses linear strategy by default, But there is a new option called serial where you can specfiy how many server your would like to process, example 3

```YAML
# Ansible Playbook
- 
  name: Deploy Web Application
  serial: 3
  hosts: server1, server2, server3
  tasks:
    - name: Install dependencies

    - name: Install Mysql
    
    - name: Start Mysql Service
  
    - name: Install Flask dependencies

    - name: Run web-server
```
So ansible runs task on 3 servers and then once that  finishes ansible proceeds to run tasks on next batch. you can also use value in %, example 30%.

> You can create your own custom strategy by developing a strategy plugin yourself.

######
---
###### 
### Forks
So how many servers can ansible talk to at a time, what if you have 100 servers and wish to run this playbook across all of them at the same time. Ansible won't unless you specfiy explicitly, ansible uses parallel processes or forks to communicate with remote host, By default ansible can create 5 forks at time and this is define in the ansbile configuration file `/etc/ansible/ansible.cfg`, You can increase the value according to your cpu power. 




