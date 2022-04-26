# Ansible Basics

## Intro

Ansible is used to configure a system automatically with minimal effort by you.
The main way Ansible is used is with “playbooks”, which are YAML files that detail what to do.

## Install

```
sudo pip install ansible  
sudo apt install cowsay (recommended)
```

(For more details, read the [Installation Guide](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) in the documentation)

## Creating an Inventory File

(For more details, read [Working with Inventory](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html) in the documentation)

An inventory file is how you tell Ansible what hosts exist, put those hosts in groups, and specify variables specific to some groups.
Inventory files can be either INI-like or YAML.
This guide will focus on the INI-like style, see the documentation for how that translates to YAML.
The default file is INI-like and located at `/etc/ansible/hosts`.

A host is specified by putting its ip on a line:

```
10.0.0.254
```

To specify multiple hostnames in one line, you can use brackets to give a range of values:

```
10.0.0.[1:7]
```

A group of hosts has a group name in brackets, followed by all hosts in that group:

```ini
[nodes]
10.0.0.1
10.0.0.2
```

Hosts can be part of multiple groups.

A group can contain other groups by adding another section with `:children` appended to the group name:

```ini
[cpdc:children]
head
nodes
```

Variables that should be used when a specific group is used can be specified in another section with `:vars` appended to the group name:

```ini
[nodes:vars]
var='value'
```

For an example, see [Appendix B](#appendix-b).

## Creating a Playbook

(For more details, read [Working With Playbooks](https://docs.ansible.com/ansible/latest/user_guide/playbooks.html) in the documentation)

A playbook starts with a `---`, indicating the start of the file.

### Creating a play

A playbook is made up of “plays”.
The plays are in a YAML array.
They specify which hosts are affected, what the remote user’s username is, any variables used during the play, what tasks will be run, and any handlers.

- A play starts by specifying which hosts will be affected using `hosts:`
- The remote user is specified with `remote_user:`
This can be done for the whole play and/or for specific tasks
- Any variables are set using `vars:`
- The tasks are listed in an array under `tasks:`
- Any handlers are listed in an array under `handlers:`
- Variables are set with key-value pairs nested under `vars:`

To use variables, enclose the variable name in double braces, such as `{{var_name}}`

### Creating a Task

A task starts with a `name:` that should describe what the task does.
This is printed to the terminal when the task is started.
The task will use a module, (e.g. `apt`, `copy`, etc.).
Modules are roughly similar to a program you would run from the command line.
See [Appendix A](#appendix-a) for a partial list of linux commands and their (roughly) associated Ansible module.

### Creating a Handler

See [Handlers: Running Operations On Change](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html#handlers-running-operations-on-change)

## ansible-pull

`ansible-pull` is a way of inverting how Ansible is usually used.
A device running `ansible-pull` will pull playbook(s) from a remote git repository and then configure itself.
This is good for updating distributed systems that may not all be up at the same time (i.e. Hardware Design Raspberry Pis).

The main playbook should be called `local.yml` and be located in the root directory of the repository.
The host should be `localhost` and `remote_user` is not needed:

```yaml
 - hosts: localhost  
   tasks: ...
```

Any files used during `copy` tasks should be in the repository.
`src` will be the path to the file relative to `local.yml`. `remote_src` is not needed.

# Appendix 1 - Ansible Modules

Note that these are not perfect one-to-one comparisons.
Check the documentation to see if the module will actually do what you need.
To find others, a good way to google them is with “ansible *program*”, which usually finds the associated module.
If you are unable to find an ansible module for your task, you can use the [shell](https://docs.ansible.com/ansible/latest/modules/shell_module.html) module which allows you to run shell commands.
Note that the `shell` module can't check if it should run its task in the same way other modules can (see [notes](https://docs.ansible.com/ansible/latest/modules/shell_module.html#notes)).

|Linux Command|Ansible Module|
|-|-|
|apt|[apt](https://docs.ansible.com/ansible/latest/modules/apt_module.html)|
|wget|[get_url](https://docs.ansible.com/ansible/latest/modules/get_url_module.html)|
|make|[make](https://docs.ansible.com/ansible/latest/modules/make_module.html)|
|useradd|[user](https://docs.ansible.com/ansible/latest/modules/user_module.html)|
|passwd|[user](https://docs.ansible.com/ansible/latest/modules/user_module.html)|
|sed|[replace](https://docs.ansible.com/ansible/latest/modules/replace_module.html)|
|tar|[unarchive](https://docs.ansible.com/ansible/latest/modules/unarchive_module.html)|
|crontab|[cron](https://docs.ansible.com/ansible/latest/modules/cron_module.html)|
|systemctl|[systemd](https://docs.ansible.com/ansible/latest/modules/systemd_module.html)|
|find|[fileglob](https://docs.ansible.com/ansible/latest/plugins/lookup/fileglob.html)|
|cp|[copy](https://docs.ansible.com/ansible/latest/modules/copy_module.html)|

# Appendix B - Examples

## Example Inventory File

```ini
[cpdc:children]
head
nodes

[head]
162.210.90.132

[nodes]
10.0.0.[1:7]

[nodes:vars]
ansible_ssh_common_args='-o ProxyCommand="ssh -W %h:%p -q narvae1@162.210.90.132"'
```

## `sudo apt update && sudo apt upgrade -y`

```yaml
---
- hosts: cpdc
  remote_user: narvae1
  tasks:
  - name: apt update/upgrade
    become: yes
    apt:
      update_cache: yes
      upgrade: yes
```
