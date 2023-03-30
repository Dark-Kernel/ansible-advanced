# Custom Modules
Modules are each items in the tasks list that performs an actual operation or check. 

**Examples:**

* firewalld
* service
* command
* debug

These are all python codes developed for certain purposes. 

---
- What if none of these modules satisfy our needs? 
- What if we want to do something custom? 
- What if we want to develop a custom module that can be used for a specific purpose in out project?

Example, 

Here we have a debug module that prints any given message onto the screen we would like to develop a custom debug module that also prints the message, but with an additional date prefixed to it.

It's a really, really simple basic use case

```python
#!/usr/bin/env python

try:
  import json
except: ImportError:
  import simplejson as json

from ansible.module_utils.basic import AnsibleModule
import time
import sys

def main():
  module = AnsibleModule{
	argument_spec = dict{
	  msg=dict{required=True, type='str'}
	}
  }

  msg = module.params['msg']

  # Successfull exit
  try: 
	print(json.dumps({
	  msg: '%s - %s' % (time.strftime("%c"), msg),
	  "changed": True
	}))
	sys.exit(0)
  except:
  # Fail exit
	print(json.dumps({
	  "failed": True,
	  "msg": "failed debugging"
	}))


if __name__=='__main__':
  main()

```
* `Ansible-module` : Ansible-module is a built-in class that helps us parse the arguments pass to our module.
* `argument_spec` : The argument spec is a directory containing a set of arguments we expect for the module. 
* `msg` : Msg is an argument that we are expecting the user to pass. And that particular argument is received in the main function, while instantiating the Ansible module like this. 

we can replace the `successfull exit` try except like this also using `Ansible module`:
```python
try: 
  # Successfull Exit
  module.exit_json(changed=True, msg='%s -%s' % (time.strftime("%c"), msg))
except:
  # Fail exit
  module.fail_json(msg="Error Message")
```
> The output of this module should strictly be JSON encoded.

This script will be placed under the modules directory, along with all the other or existing modules, or this script can be placed under a library directory under your project and you can point Ansible to this library directory using an environment variable.



As per the Ansbile best practices, there are a few more items required for a complete Ansible module.

* Documentation
* Examples


#### Documentation

When you run the Ansible document with a module name, it lists the help filem or man page, for that particular module.

```bash
ansible-doc debug # example
```
It contains the description of the module, what is does, and what parameters can be passed to it.

This documentation is generated from a documentation block in the module code. In the module code, before the main function, you could create a documentation block like this.

```python
#!/usr/bin/python


DOCUMENTATION = """
---
module: custom_debug
short_description: A custom debug module 
description: 
  - print custom debug message
version_added: 0.1
author: John doe, @johndoe
notes:
requirements:
options:
  msg:
	description: Message to print
	required: True
"""

... 
```
This particular block is in `YAML` format and it has the module name, a short description, a long description, the version, the author's name, some requirements, and the options, which are the arguments that are passed to this particular module. 


#### Examples

`ansible-doc` also outputs few example use cases of the module. This documention is generated from an example blocks in the module code.

```python
...

EXAMPLES = """
# Example
custom_debug:
  msg: This is test message
"""

...
```
This is what which will be used to show an example usage of the module to the user when he runs the ansible-doc debug command.

So where are these files created?

By default all built-in modules are located under `modules` directory in Ansible directory under `dist-packages` in Python. Full path: `/usr/lib/python2.7/dist-packages/ansible/modules`

If you need the custom module to be made available for a role, you could put this under a library directory in your role and it will be automatically be made available to the playbook. Or, if you need to make it available for multiple projects you could store it anywhere and specify the path using `ansible_library` environment variable.

---

######
You can use [playAble](https://hub.docker.com/r/mmumshad/ansible-playable) tool to build own modules using simple UI.

Look into [Ansible Docs](https://docs.ansible.com/ansible/latest/dev_guide/developing_modules_general.html) for more info.










