.. _service:

service
```````

Controls services on remote machines.

+--------------------+----------+---------+----------------------------------------------------------------------------+
| parameter          | required | default | comments                                                                   |
+====================+==========+=========+============================================================================+
| name               | yes      |         | name of the service                                                        |
+--------------------+----------+---------+----------------------------------------------------------------------------+
| state              | no       | started | 'started', 'stopped', 'reloaded', or 'restarted'.  Started/stopped are     |
|                    |          |         | idempotent actions that will not run commands unless neccessary.           |
|                    |          |         | 'restarted' will always bounce the service, 'reloaded' will always reload. |
+--------------------+----------+---------+----------------------------------------------------------------------------+
| pattern            | no       |         | (new in 0.7) if the service does not respond to the status command,        |
|                    |          |         | name a substring to look for as would be found in the output of the 'ps'   |
|                    |          |         | command as a stand-in for a status result.  If the string is found, the    |
|                    |          |         | service will be assumed to be running.                                     |
+--------------------+----------+---------+----------------------------------------------------------------------------+
| enabled            | no       |         | Whether the service should start on boot.  Either 'yes' or 'no'.           |
+--------------------+----------+---------+----------------------------------------------------------------------------+

Example actions from Ansible :doc:`playbooks`::

    service name=httpd state=started
    service name=httpd state=stopped
    service name=httpd state=restarted
    service name=httpd state=reloaded
    service name=foo pattern=/usr/bin/foo state=started

One caveat to keep in mind when using the `service` module: In some cases the init.d/service may attempt to
background a process which dies as soon as the ansible shell process terminates (i.e., as soon as the service
task is completed).

The way around this limitation is to use the `command` module by passing the service startup script a `nohup`.
For example::

    command /usr/bin/nohup /sbin/service/rabbitmq-server start

