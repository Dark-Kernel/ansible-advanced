# Jinja2 Templating

It is a full featured template engine for python, has a wide range feature. It's not something specific to ansible, It is generic template engine used for different purpose. And it happens to be ansible's choice to be used internally for templating.

Templating
: It is a process of generating dynamic content or expressions.

Example, we have a sentence

```
My name is John doe
```
Here the name itself is a variable, so it should form the sentence with the value in the variable and  not the static name.

```jinja 
My name is {{ my_name }}
```
This braces indicates that the text inside it is a variable. 

Every expression in ansible will evaluated as a template before being processed by a task.

An easy to test and play around with the jinja2 templating using ansible is to use `debug` module, the debug module lets you print the simple message out to screen.

```YAML
# Ansible playbook

-
  name: Test Template playbook
  hosts: localhost
  vars:
    my_name: John Doe
  tasks:
    - debug:
        msg: "My name is {{ my_name }}"
```

###### 
## String Manupulation

#### Filters: 
```jinja
The name is {{ my_name }} # => The name is John
```
* Uppercase: We could simple append the keyword `upper`

```jinja
The name is {{ my_name | upper }} # => The name is JOHN
```
* Lowercase: We could simple append the keyword `lower` 
```jinja
The name is {{ my_name | lower }} # => The name is john
```
* Titlecase: To convert string to title case we use `title` keyword.
```jinja
The name is {{ my_name | title }} # => The name is John
```
* replace: we can use `replace` to replace a part of a string
```jinja
The name is {{ my_name | replace ("John","Johny") }} # => The name is Johny
```
* default: If you don't want the playbook to fail if variable is not defined you can set the default value.
```jinja
The name is {{ first_name | default("Paul") }} {{ my_name }} # => The name is Paul Johny
```

#### List & Set filters

* min: If you have a set or an array and would like to get the lowest number from that you can use `min` filter
```jinja
{{ [1,2,3] | min }} # => 1
```

* max: You could use `max` filter to get the highest number.
```jinja
{{ [1,2,3] | max }} # => 3
```

* unique: In case you have an array with duplicate value you can use `unique` filter
```jinja
{{ [1,2,3,2] | unique }} # => 1,2,3
```

* union: If you have two seperate arrays and if you would to get the unique values across both arrays you can use `union` filter.
```jinja
{{ [1,2,3,4] | union([4,5]) }} # => 1,2,3,4,5
```
* intersect: If you would like to find the common items you can use `intersect` filter.
```jinja
{{ [1,2,3,4] | intersect([4,5]) }} # => 4
```
* random: If you would like to generate a random number you can use `random` filter.
```jinja
{{ 100 | random }} # => Random numbers 0-100
```
* join: If you would like to join items and create a single sentence you can use `join` filter with the seperator.
```jinja
{{ ["The","name","is","John"] | join(" ") }} # => The name is John
```
#### File filters

* basename: To get the file name from the full path we can use `basename` filter for linux and `win_basename` for windows.
```jinja
{{ "/etc/hosts" | basename }} # => hosts @linux
{{ "c:\windows\hosts" | win_basename }} # => hosts @windows
```
* win_splitdrive: To get the drive letter form the given path, and it returns array.
```jinja
# Returns array with two element.
{{ "c:\windows\hosts" | win_splitdrive }} # => ["c:","\windows\hosts"]

# We can use `first` filter to get the first element from array
{{ "c:\windows\hosts" | win_splitdrive | first }} # => "c:"

# We can get the last element using `last` filter
{{ "c:\windows\hosts" | win_splitdrive | last }} # => "\windows\hosts"
```

There are many more filter available on Documentation.

* [Jinja2 Documentation](https://jinja.palletsprojects.com/en/3.1.x/)
* [Ansible Documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_templating.html)











