# Plugins
The core ansible engine uses a variety of plugins to perform various operations.

For example,
* It uses action plugins to invoke modules. 
* It uses connection plugins to establish communication with hosts.
* It uses Filter plugins to manipulate data. 
* Lookup plugins to work with data from external sources such as, CSV lookup that we saw earlier.
* Strategy plugins to control flow and execution of play
* Callback plugins to handle events or results of execution.

There are number of other plugins as well. This architecture makes ansible highly extensible as we could easilt create additional custom plug-ins to suit our needs.

Let's look at creating a couple of custom plugins.

######
#### Custom Filter

Let's create a simple custom filter that we can use in the playbook to get the average of a list.

We have a simple playbook that prints the average of a given list of numbers.

```YAML
# playbook.yml
-
  name: Print average marks
  hosts: localhost
  vars:
	marks:
	  - 10
	  - 20
	  - 30
	  - 40
  
  tasks:
	- debug: 
		msg: '{{ marks | average }}'
```

Note that average is the filter we need to develop as it is not available by default in Ansible.

To do this, create a simple Python file named `average.py`
```python
# /filter_plugins/average.py

def average(list):
  # find avg of a list of numbers
  return sum(list) / float(len(list))


class FilerModule(object):
  # Query filter

  def filters(self):
	return {
	  "average": average 
	}
```
`class FilterModule`: It has a filters method that returns a dictionary of filter names and filter functions. 

To run the playbook now, we need to remember to set an environment variable called `Ansible_filter_plugins` and point it to the filter plugins directory. This is how ansible knows where to find new plugins that we created. Or you can use the default plugins path under python library folder.

```bash
export ANSIBLE_FILTER_PLUGINS=/filter_plugins;
ansible-playbook playbook.yml
```

######
### Callback Plugins
Callback plugins is another type of plugins. The output that we usually see from Ansible is a result of the callback plugin. It is a callback plugin that brings the information that you see in the output format in screen at the end of each task or play execution.  This default output is called **skippy.**
```

PLAY [Test connectivity to target servers] *****************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************
fatal: [target2]: FAILED! => {"msg": "Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host."}
ok: [target1]

TASK [Ping test] *******************************************************************************************************************************************************************************
ok: [target1]

PLAY RECAP *************************************************************************************************************************************************************************************
target1                    : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target2                    : ok=0    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
```

We could change the callback plugin to use JSON instead of printing it in a text format. For this you need to set the environment variable called `ANSIBLE_STDOUT_CALLBACK`. 

```bash
export ANSIBLE_STDOUT_CALLBACK=json
ansible-playbook playbook.yml
```
And it will now print the results of the execution in a JSON format.

**Usecases**

callback plugins are not only used to print information out to the screen. You can use other actions :

* Mail: To send errors to notification emails.
* Hipchat: Integrate with application by sending status updates during execution
* Jabber: To Integrate with application like Jabber or Logstash or Slack.
* Timer: The timer callback plug-in calculates the execution time and prints it to the console at the end of the execution.

So, these are the few built-in callback plugins available. Get some of [here](https://github.com/ansible/ansible/tree/devel/lib/ansible/plugins/callback).


