.. Director documentation master file, created by sphinx-quickstart on Sat Sep 27 13:23:22 2008.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Ansible
=======

Ansible is a radically simple deployment, configuration, and command
execution framework.  Other tools in this space have been too
complicated for too long, require too much bootstrapping, and have too
much learning curve.  Ansible is dead simple and painless to extend.
For comparison, Puppet and Chef have about 60k lines of code.
Ansible's core is a little over 1000 lines.

Ansible isn't just for configuration -- it's also great for Ad-Hoc
tasks, quickly firing off commands against nodes.  Where Ansible
excels though, is expressing complex multi-node deployment processes,
executing complex sequences of commands on different hosts through
:doc:`playbooks`.

Extending ansible does not require programming in any particular
language -- you can write modules as scripts or programs that return
simple JSON.  It's also trivially easy to just execute useful shell
commands.

Why use Ansible versus something else?  (Puppet, Chef, Fabric,
Capistrano, mCollective, Func, SaltStack, etc?) Ansible will have far
less code, it will be (by extension) more correct, and it will be the
easiest thing to hack on and use you'll ever see -- regardless of your
favorite language of choice.

Systems management doesn't have to be complicated.  Ansible's docs
will remain short & simple, and the source will be blindingly obvious.


Design Goals
````````````

* Dead simple setup
* Super fast & parallel by default
* No server or client daemons; use existing SSHd out of the box
* No additional software required on client boxes
* Modules can be written in ANY language
* Awesome API for creating very powerful distributed scripts
* Be very usable as non-root
* Create the easiest config management system to use, ever.

Communicate and Get Involved
````````````````````````````

Your ideas and contributions are welcome.  We're also happy to help
you with questions about Ansible.

* Join the `ansible-project mailing list <http://groups.google.com/group/ansible-project>`_ on Google Groups
* Join `#ansible <irc://irc.freenode.net/#ansible>`_ on the `freenode IRC network <http://freenode.net/>`_
* Visit the `project page <https://github.com/ansible/ansible>`_ on Github

  - View the `issue tracker <https://github.com/ansible/ansible/issues>`_


Contents
========

.. toctree::
   :maxdepth: 3

   gettingstarted
   patterns
   examples
   modules
   YAMLScripts
   playbooks
   api
   man

About the Author
````````````````

Ansible was originally developed by Michael DeHaan, a Raleigh, NC
based software developer and architect.  He created other popular
DevOps programs such as Cobbler, the popular Linux install server.
Cobbler is used to deploy mission critical systems all over the
planet, in industries ranging from massively multiplayer gaming, core
internet infrastructure, finance, chip design, and more.  Michael also
helped co-author of Func, a precursor to Ansible, which is used to
orchestrate systems in lots of diverse places.  He's worked on systems
software for IBM, Motorola, Red Hat's Emerging Technologies Group,
Puppet Labs, and rPath.
