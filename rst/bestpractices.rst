Best Practices
==============

Here are some tips for making the most of Ansible.

Group By Roles
++++++++++++++

A system can be in multiple groups.  See :doc:`patterns`.   Having groups named after things like
'webservers' and 'dbservers' is repeated in the examples because it's a very powerful concept.

This allows playbooks to target machines based on role, as well as to assign role specific variables
using the group variable system.

Directory Organization
++++++++++++++++++++++

Playbooks should be organized like this::
 
    (root of source control repository -- I keep mine at /etc/ansible/playbooks/)

        global_varibles.yml  # global variables for all playbooks
        webserver/           # each playbook has a directory

            setup.yml        # playbook to manage the service
            stop.yml         # playbook to halt the service (optional)

            files/           # files to get copied without doing templating
               httpd.conf

            templates/       # ansible templates which will be modified
               app-config.conf

            vars/            # per-playbook variables, also can over-ride globals
               main.yml

            handlers/        # handlers defined from any of the tasks below
               main.yml

            tasks/
               setup.yml     # the actual tasks to run in the playbook
               stop.yml      # tasks to undo the setup tasks, called from webserver/stop.yml (optional)

Any directories or files not needed can be omitted.  Not all modules may require `vars` or `files` sections, though most
will require `handlers`, `tasks`, and `templates`.  To review what each of these sections do, see :doc:`playbooks` and :doc:`playbooks2`.

The global_varibles.yml file is for varibles which should be shared by every playbook should look something like this::

    ---
    is_cent6: "'$ansible_distribution' == 'CentOS' and '$ansible_distribution_version'.startswith('6')"
    is_cent5: "'$ansible_distribution' == 'CentOS' and '$ansible_distribution_version'.startswith('5')"
    ansible_master: 10.0.0.10

The webserver/setup.yml playbook would be as simple as::

    ---
    - hosts: webservers
      user: root

      vars_files
        - ../global_varibles.yml
        - vars/main.yml
      tasks:
        - include: tasks/setup.yml
      handlers:
        - include: handlers/main.yml

The tasks are individually broken out in 'webserver/tasks/setup.yml' and should have tasks like these::

     ---
     - name: ensure httpd package is installed
       action: yum pkg=httpd state=latest
       notify:
         - restart httpd

     - name: ensure httpd conf file is installed
       action: copy src=files/httpd.conf dest=/etc/httpd/conf/httpd.conf
       notify:
         - reload httpd

     - name: ensure centos5 conf file is installed only on centos5
       action: copy src=files/centos5.conf dest=/etc/httpd/conf.d/centos5.conf
       only_if: '$is_centos5'
       notify:
         - reload httpd

     - name: ensure centos6 conf file is installed only on centos6
       action: copy src=files/centos6.conf dest=/etc/httpd/conf.d/centos6.conf
       only_if: '$is_centos6'
       notify:
         - reload httpd

     - name: ensure the web app config file is installed
       action template src=templates/app-config.conf dest=/var/www/app-config.conf owner=apache group=apache mode=600
       notify
         - reload httpd

     - name: checkout the current version of the web app using git
       action git repo=git@example.com:my-webapp.git dest=/var/www/html/ branch=master version=HEAD

Handlers, which are common to all task files, should exists in are contained in 'webserver/handlers/main.yml'.
As a reminder, handlers are mostly just used to notify services to restart when things change, and these are described in :doc:`playbooks`.
They should contain things like this::

    ---
    - name: restart httpd
      action: service name=httpd state=restarted

    - name: reload httpd
      action: service name=httpd state=resloaded

Notice the difference between the 'reload httpd' and 'restart httpd' handlers.
Including more than one setup file or more than one handlers file is of course legal.

The varibles which are not defined in the global_varibles.yml file should be defined in the file vars/main.yml and should look something like this::

     ---
     is_firstserver: "'$ansible_fqdn' == 'foo1.example.com'"

You can also over-ride the varibles from the global file by setting them again in vars/main.yml::

     ---
     # override the master server
     ansible_master: 192.168.122.121
     
Having playbooks be able to include other playbooks is coming in release 0.5.

Until then, to manage your entire site, simply execute all of your playbooks together, in the order desired.
You don't have to do this though. It's fine to select sections of your infrastructure to manage at a single time.
You may wish to construct simple shell scripts to wrap calls to ansible-playbook.

Miscellaneous Tips
++++++++++++++++++

When you can do something simply, do something simply.  Do not reach to use every feature of Ansible together, all
at once.  Use what works for you.  For example, you should probably not need ``vars``, ``vars_files``, ``vars_prompt`` and ``--extra-vars`` all at once, while also using an external inventory file.

Optimize for readability.  Whitespace between sections of YAML documents and in between tasks is strongly encouraged,
as is usage of YAML comments, which start with "#".  It is also useful to comment at the top of each file the purpose of the individual file and the author, including email address.

It is possible to leave off the "name" for a given task, though it is recommended to provide
a descriptive comment about why something is being done instead.

Use version control.  Keep your playbooks and inventory file in git (or another version control system), and commit when you make changes to them.
This way you have an audit trail describing when and why you changed the rules automating your infrastructure.

Resist the urge to write the same playbooks and configuration files for heterogeneous distributions.  While lots of software packages claim to make this easy on you, the configuration files are often quite different, to the point where it would be easier to treat them as different playbooks.  This is why, for example, Ansible has a seperate 'yum' and 'apt' module.  Yum and apt have different capabilities, and we don't want to code for the least common denominator.

Use variables for user tunable settings versus having constants in the tasks file or templates, so that it is easy to reconfigure a playbook.  Think about this as exposing the knobs to things you would like to tweak.

Since a system can be in more than one group, if you have multiple datacenters or sites, consider putting systems into groups by role, but also different groups by geography.  This allows you to assign different variables to different geographies.

.. seealso::

   :doc:`YAMLSyntax`
       Learn about YAML syntax
   :doc:`playbooks`
       Review the basic playbook features
   :doc:`modules`
       Learn about available modules
   :doc:`moduledev`
       Learn how to extend Ansible by writing your own modules
   :doc:`patterns`
       Learn about how to select hosts
   `Github examples directory <https://github.com/ansible/ansible/tree/master/examples/playbooks>`_
       Complete playbook files from the github project source
   `Mailing List <http://groups.google.com/group/ansible-project>`_
       Questions? Help? Ideas?  Stop by the list on Google Groups


