# Lookups

### File lookup
So far we are saving credentials for our target serves ion invetory file. What if this credentials is already available on file?.

Example, We have one csv file containing credentials, The first column being the `hostname` and second column `password`. To ready the content of the file while ansible playbook is running and to get the password assosicated to a host, In this case we can use `lookup` plugin.

```csv
# CSV File - credentials.csv 
Hostname, Password
target1, pass
target2, pass
```
This is how we will use lookup plugin.
```YAML
{{ lookup('csvfile', 'target1 file=/tmp/credentials.csv delimiter=,') }} # => pass
```
* The first argument we pass to lookup is type of file Eg. `csvfile`
* The second argument is value to lookup Eg. here we looking info for target1 server. Followed by the `file` to lookup and finally `delimiter`. 
######
---
######
There are few other lookup plugins available such as:
* `INI`
* `DNS`
* `MongoDB`

Information about this can be seen [here](https://docs.ansible.com/archive/ansible/2.3/playbooks_lookups.html) 

