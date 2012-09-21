.. _patterns:

Inventory & Patterns
====================

Ansible works against multiple systems in your infrastructure at the
same time.  It does this by selecting portions of systems listed in
Ansible's inventory file, which defaults to /etc/ansible/hosts.

.. contents:: `Table of contents`
   :depth: 2
   :backlinks: top

.. _inventoryformat:

Hosts and Groups
++++++++++++++++

The format for /etc/ansible/hosts is an INI format and looks like this::

    mail.example.com

    [webservers]
    foo.example.com
    bar.example.com

    [dbservers]
    one.example.com
    two.example.com
    three.example.com

The things in brackets are group names. You don't have to have them,
but they are useful.

If you have hosts that run on non-standard SSH ports you can put the port number
after the hostname with a colon.  

    four.example.com:5309

Ansible considers each hostname as unique, since it is used to do DNS lookup. This
is important to keep in mind when testing with different ports on the same host.
For example, the following entries would resolve to the same host::

    192.168.0.10:22
    192.168.0.10:8022
    192.168.0.10:48022

This problem can be demonstrated by the following test. Say 192.168.0.10 hosts two
virtualboxes, Ubunto on port 8022 and CentOS on port 48022. Say the inventory then 
makes this OS differentiation::

    [ubuntu]
    192.168.0.10:8022

    [centos]
    192.168.0.10:48022

Running `ansible -i my.hosts ubuntu -m setup | grep -i "ansible_distribution\""`
returns `"ansible_distribution": "Ubuntu"`.

So let's see what happens for the centos alias::

    ansible -i my.hosts centos -m setup | grep -i "ansible_distribution\""

Unexpectedly, this also returns `"ansible_distribution": "Ubuntu"`.

To get around this problem, keep in mind that DNS the reason ansible is doing this.
We just need to have unique hostname entries for each port. This is done by adding 
hostnames into your /etc/hosts folder and then using specific ports in your inventory::

    # /etc/hosts
    192.168.0.10 my-centos myubuntu

    # my.hosts inventory file
    [ubuntu]
    my-ubuntu:8022
    [centos]
    my-centos:48022
    
Ansible will now return what you expect.

In 0.6 and later, if you have a lot of hosts following similar patterns you can do this::

    [webservers]
    www[01-50].example.com

Leading zeros can be included or removed, as desired, and the ranges are inclusive.

Selecting Targets
+++++++++++++++++

We'll go over how to use the command line in :doc:`examples` section, however, basically it looks like this::

    ansible <pattern_goes_here> -m <module_name> -a <arguments>
    
Such as::

    ansible webservers -m service -a "name=httpd state=restarted"

Within :doc:`playbooks`, these patterns can be used for even greater purposes.

Anyway, to use Ansible, you'll first need to know how to tell Ansible which hosts in your inventory file to talk to.
This is done by designating particular host names or groups of hosts.

The following patterns target all hosts in the inventory file::

    all
    *    

Basically 'all' is an alias for '*'.  It is also possible to address a specific host or hosts::

    one.example.com
    one.example.com:two.example.com
    192.168.1.50
    192.168.1.*
 
The following patterns address one or more groups, which are denoted
with the aforementioned bracket headers in the inventory file::

    webservers
    webservers:dbservers

You can exclude groups as well, for instance, all webservers not in Phoenix::

    webservers:!phoenix

Individual host names (or IPs), but not groups, can also be referenced using
wildcards::

    *.example.com
    *.com

It's also ok to mix wildcard patterns and groups at the same time::

    one*.com:dbservers

Easy enough.  See :doc:`examples` and then :doc:`playbooks` for how to do things to selected hosts.

Host Variables
++++++++++++++

It is easy to assign variables to hosts that will be used later in playbooks::
 
   [atlanta]
   host1 http_port=80 maxRequestsPerChild=808
   host2 http_port=303 maxRequestsPerChild=909


Group Variables
+++++++++++++++

Variables can also be applied to an entire group at once::

   [atlanta]
   host1
   host2

   [atlanta:vars]
   ntp_server=ntp.atlanta.example.com
   proxy=proxy.atlanta.example.com

Groups of Groups, and Group Variables
+++++++++++++++++++++++++++++++++++++

It is also possible to make groups of groups and assign
variables to groups.  These variables can be used by /usr/bin/ansible-playbook, but not
/usr/bin/ansible::

   [atlanta]
   host1
   host2

   [raleigh]
   host2
   host3

   [southeast:children]
   atlanta
   raleigh

   [southeast:vars]
   some_server=foo.southeast.example.com
   halon_system_timeout=30
   self_destruct_countdown=60
   escape_pods=2

   [usa:children]
   southeast
   northeast
   southwest
   southeast

If you need to store lists or hash data, or prefer to keep host and group specific variables
seperate from the inventory file, see the next section.

Splitting Out Host and Group Specific Data
++++++++++++++++++++++++++++++++++++++++++

.. versionadded:: 0.6

In addition to the storing variables directly in the INI file, host
and group variables can be stored in individual files relative to the
inventory file.  These variable files are in YAML format.

Assuming the inventory file path is::

    /etc/ansible/hosts

If the host is named 'foosball', and in groups 'raleigh' and 'webservers', variables
in YAML files at the following locations will be made available to the host::

    /etc/ansible/group_vars/raleigh
    /etc/ansible/group_vars/webservers
    /etc/ansible/host_vars/foosball

For instance, suppose you have hosts grouped by datacenter, and each datacenter
uses some different servers.  The data in the groupfile '/etc/ansible/group_vars/raleigh' for
the 'raleigh' group might look like::

    ---
    ntp_server: acme.example.org
    database_server: storage.example.org

It is ok if these files do not exist, this is an optional feature.

Tip: Keeping your inventory file and variables in a git repo (or other version control) 
is an excellent way to track changes to your inventory and host variables.

.. versionadded:: 0.5
   If you ever have two python interpreters on a system, set a
   variable called 'ansible_python_interpreter' to the Python
   interpreter path you would like to use.

YAML Inventory
++++++++++++++

.. deprecated:: 0.7

Ansible's YAML inventory format is deprecated and will be removed in
Ansible 0.7.  Ansible 0.6 includes a `conversion script
<https://github.com/ansible/ansible/blob/devel/examples/scripts/yaml_to_ini.py>`_.

Usage::

    yaml_to_ini.py /etc/ansible/hosts

.. seealso::

   :doc:`examples`
       Examples of basic commands
   :doc:`playbooks`
       Learning ansible's configuration management language
   `Mailing List <http://groups.google.com/group/ansible-project>`_
       Questions? Help? Ideas?  Stop by the list on Google Groups
   `irc.freenode.net <http://irc.freenode.net>`_
       #ansible IRC chat channel

