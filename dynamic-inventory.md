# Dynamic Inventory

So far we are using inventory file in plain text like this
```txt
db_server ansible_host=192.168.2.4
web_server ansible_host-192.18.2.5
```
This is the simplest way of defining list of servers.

In case we may want to pull this information down from external servers like `CMDB` database or from cloud api, that's where Dynamic Inventory comes in handy.

The Dynamic Inventory is inventory information that ansible retrieves programatically when the ansible playbook is run, as opposed to us defining it in a static inventory text file. 

Example

we have a static inventory file:
```txt
# inventory.txt
db_server ansible_host=192.168.2.4
web_server ansible_host-192.18.2.5
```
The command to execute an ansible playbook using this is to pass in the inventory.txt file as a parameter to the -i argument.
```bash
ansible-playbook playbook.yml -i inventory.txt
```

  The simplest way to understand Dynamic Inventory is to convert the the same inventory file into python script. To use a dynamic inventory file using a script, the command looks the same except for the file name changing to a script with the `.py` extension.
```bash
ansible-playbook playbook.yml -i inventory.py
```

So we have a new file now called `inventory.py`, which is a python script and all doing for is to print the same inventory details in a JSON format. 
```python
#!/usr/bin/env python

import json

# Get the inventory data from source - CMDB or any other API
def get_inventory_data():
  return {
	"databases": {
	  "hosts": ["db_server"],
	  "vars": {
		"ansible_ssh_host": "192.168.1.1",
		"ansible_ssh_pass": "Passw0rd"
	  }
	},
	"web": {
	  "hosts": ["web_server"],
	  "vars": {
		"ansible_ssh_host": "192.168.1.2",
		"ansible_ssh_pass": "Passw0rd"
	  }
	}
  }


# default main function
if __name__=="__main__":
  inventory_data = get_inventory_data()
  print(json.dumps(inventory_data))
```
What Ansible does internally is to run this inventory script and use the output as inventory information. 


There are couple of things more to handle, when ansible executes the script it will pass in one of the two arguments. 

```bash
./inventory.py --list

./inventory.py --host web # any hostname
```

The first one should output a JSON encoded dictionary of all groups, just like what we have now. 
In case of the second, it must print a dictionary of variables to make available to that particular host.

For example, web has these two variables available
```JSON
{
 "ansible_ssh_host": "192.168.1.2",
 "ansible_ssh_pass": "Passw0rd"
}
```
To Handle these arguments we will add new function called read CLI args, and inside that, read the two arguments Then we will modify our main function to read the CLI arguments first, and print inventory data in case of list, and some empty data in case of host.

```python
#!/usr/bin/env python

import json

# Get the inventory data from source - CMDB or any other API
def get_inventory_data():
  return {
	"databases": {
	  "hosts": ["db_server"],
	  "vars": {
		"ansible_ssh_host": "192.168.1.1",
		"ansible_ssh_pass": "Passw0rd"
	  }
	},
	"web": {
	  "hosts": ["web_server"],
	  "vars": {
		"ansible_ssh_host": "192.168.1.2",
		"ansible_ssh_pass": "Passw0rd"
	  }
	}
  }

def read_cli_args():
  global args
  parser = argparse.ArgumentParser()
  parser.add_argument("--list", action='store_true')
  parser.add_argument("--host", action='store')
  args = parser.parse_args()

# default main function
if __name__=="__main__":
  global args
  read_cli_args()
  inventory_data = get_inventory_data()
  if args and args.list:
	print(json.dumps(inventory_data))
  elif args.host:
	print(json.dumps({'_meta': {'hostvars': {} }}))
	
```

---
#### Examples

* Cobbler
* AWS
* Azure
* Google Compute Engine
* Openstack
* VMware
* Docker

A full list can be found at [Ansible github page](https://github.com/ansbile/ansible/free/devel/contrib/inventory)

