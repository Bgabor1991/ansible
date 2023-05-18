Ansible
=======

Ansible is an automation platform for application deployment,
configuration management, update and maintain workstations and servers.
Ansible is agentless, you don't need any agent on the workstations, it
relying on SSH connections.

Example use cases:

-  Install and update packages

-  Configure system files

-  Start/stop/restart system services

-  Deploy applications

-  Create users

Ansible basics
--------------


Ansible configuration file: 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Location: /etc/ansible/ansible.conf

You can define configurations for ansible like:

Ask_sudo_pass – Give the sudo password for ansible, it’s necessary if
the workstation ask for password when performing sudo command

Host_key_checking – Ignore the key checking when you attempt the first
connect to a workstation

Inventory – Define the location for the inventory file. The default is
/etc/ansible/hosts

Log_path – Define the location of the logfile for ansible. Make sure
ansible has permission to the logfile.

Private_key_file – You can set the private SSH key location.

Remote_user - Set the remote username, ansible use the current user if
it’s not defined.



Inventory file:
~~~~~~~~~~~~~~~

Default location: /etc/ansible/hosts

In this file you define the workstations/servers IPs or hostnames. You
can group them or just list them in the file.

In the CLI you can set different inventory for the current playbook than
the default ‘ansible-playbook -I my_inventory my_playbook.yml’

Structure:

[webservers]

192.168.0.1

192.168.0.2

[databases]

192.168.0.3

192.168.0.4

[fileserver]

192.168.0.5



Ad hoc commands:
~~~~~~~~~~~~~~~~

+-----------------------------------+-----------------------------------+
| Ansible –list-hosts all           | List all the hosts in the         |
|                                   | inventory                         |
+===================================+===================================+
| Ansible all -m ping               | Check the connection towards the  |
|                                   | servers                           |
+-----------------------------------+-----------------------------------+
| Ansible all -m setup              | Get facts about the machines      |
|                                   | (System, network, os, etc.) You   |
|                                   | can use “\| grep “ to filter the  |
|                                   | result                            |
+-----------------------------------+-----------------------------------+
| Ansible all -m apt -a “name=nginx | Install the nginx package on all  |
| state=latest” –become             | workstations                      |
+-----------------------------------+-----------------------------------+

Option examples in ad hoc commands:

+-----------------------------------+-----------------------------------+
| All/webservers/databases          | Define which workstations to work |
|                                   | with                              |
+===================================+===================================+
| -m MODULE_NAME                    | Name of the module                |
+-----------------------------------+-----------------------------------+
| -a ARGUMENT                       | Arguments for the module          |
+-----------------------------------+-----------------------------------+
| -I INVENTORY_FILE                 | Specify the inventory file        |
+-----------------------------------+-----------------------------------+
| --limit IP_ADDRESS                | Run the play for only on a        |
|                                   | particular workstation            |
+-----------------------------------+-----------------------------------+
| --ask-pass                        | Give the password for             |
|                                   | authentication                    |
+-----------------------------------+-----------------------------------+
| --become                          | Run the command as superuser      |
+-----------------------------------+-----------------------------------+
| --ask-become-pass                 | Give the password to ansible for  |
|                                   | sudo                              |
+-----------------------------------+-----------------------------------+
| --private-key                     | Use SSH key for authentication    |
+-----------------------------------+-----------------------------------+
| --check                           | Check the play without making any |
|                                   | changes                           |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+

Playbooks
---------

Ansible use yaml files for the playbooks. In this file you define
everything for the current play.

//Be careful, yaml files are very indentation sensitive//

The main structure:

---

-  Name: My example playbook

Hosts: all

Become: yes

Tasks:

-  Name: install nginx

..

   Package:

   Name: nginx

   State: latest

+-----------------+----------------------------------------------------+
| Name            | Name of the playbook                               |
+=================+====================================================+
| Hosts           | On which hosts need to ansible execute the tasks   |
|                 |                                                    |
|                 | All/webservers/databases                           |
+-----------------+----------------------------------------------------+
| Become          | Become superuser the execute the tasks             |
+-----------------+----------------------------------------------------+
| Tasks           | Here you can define one or more task for this play |
+-----------------+----------------------------------------------------+
| Name            | The name of the task                               |
+-----------------+----------------------------------------------------+
| MODULE_NAME     | Here you can give which ansible built-in module    |
|                 | you want to use                                    |
|                 |                                                    |
|                 | Like: apt, yum, package, copy, files, user,        |
|                 | service, etc.                                      |
+-----------------+----------------------------------------------------+
| M               | In this section, you set the arguments for the     |
| ODULE_ARGUMENTS | module                                             |
|                 |                                                    |
|                 | Package: name and state                            |
|                 |                                                    |
|                 | User: name, state                                  |
|                 |                                                    |
|                 | Copy: source, destination and file permission      |
|                 |                                                    |
|                 | Service: name and state                            |
+-----------------+----------------------------------------------------+
| When            | When this tasks need to be executed. Like when a   |
|                 | host fact is true                                  |
|                 |                                                    |
|                 | When: ansible_facts['os_family']|lower == 'redhat' |
+-----------------+----------------------------------------------------+
| Notify          | Trigger a handler when the task changed. For       |
|                 | example the sshd config file was modified, restart |
|                 | the sshd service.                                  |
+-----------------+----------------------------------------------------+



Roles
-----

| Roles give you a structured way to define different collections of
  tasks, files, handlers, variables, etc.
| With this technic, you are able to write more readable playbooks.
  Ansible will look for main.yml in every folder.

The structure of a role:

/etc/ansible/roles/MY_ROLE_NAME

Here you can define the parameters for the role like:

/vars/main.yml

Custom variables for the task like a default directory

/handlers/main.yml

Define a handler that is triggerd by our task. For example restart sshd

/files/

Folder for the files if you use any in the play

/templates/nginx.conf.j2

Set a custom configuration file based on our template. Here you can use
variables based on variables defined in the vars folder

/tasks/main.yml

In this file, you define the tasks for the roles




Templates
---------

Templates allow you to create or overwrite an existing file on the nodes
with predefined variables. To make a template, you need to save your for
example configuration file to ‘sshd_config.j2’. Ansible will fill the
variables place with the host or role specific vars.
