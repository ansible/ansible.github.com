.. _patterns:

The Inventory File, Patterns, and Groups
========================================

How to select hosts you wish to manage

.. seealso::

   :doc:`examples`
       Examples of basic commands
   :doc:`playbooks`
       Learning ansible's configuration management language


.. _inventoryformat:

Inventory File Format
+++++++++++++++++++++

Ansible works against multiple systems in your infrastructure at the
same time.  It does this by selecting portions of systems listed in
Ansible's inventory file, which defaults to /etc/ansible/hosts, and
looks like this::

    mail.example.com

    [webservers]
    foo.example.com
    bar.example.com

    [dbservers]
    one.example.com
    two.example.com
    three.example.com


Selecting Targets
+++++++++++++++++

These patterns target all hosts in the inventory file::

    all
    *    

It is also possible to address specific hosts::

    one.example.com
    one.example.com:two.example.com
 

The following patterns address one or more groups, which are denoted
with the bracket headers in the inventory file::

    webservers
    webservers:dbservers

Individual hosts, but not groups, can also be referenced using
wildcards::

    *.example.com
    *.com

It's also ok to mix wildcard patterns and groups at the same time::

    one*.com:dbservers

.. note::
    It is not possible to target a host not in the inventory file.
