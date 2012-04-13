Frequently Asked Questions
==========================

What inspired Ansible?
----------------------

Back when I worked for Red Hat and working on `Cobbler <http://cobbler.github.com/>`_, several of us identified a gap between
provisioning (Cobbler) and configuration management solutions (cfengine, Puppet, etc).
There was a need for a way to do ad-hoc tasks efficiently, and various parallel
SSH scripts were not API based enough for us.  So we (Adrian Likins, Seth Vidal, and I) 
created `Func <http://fedorahosted.org/func>`_ -- a secure distributed command framework.

I always wanted to have a configuration management system built on Func, but never
built it due to needing to spend time on Cobbler and other projects.  
In the meantime, a John Eckersberg developed Taboot, 
a deployment framework of sorts that sat on top of Func, using a YAML syntax very
much like what Ansible now has in :doc:`playbooks`.

After trying to get Func running again recently at a new company, I got tired
of some SSL and DNS issues and decided to create something a bit simpler, taking
all of the good ideas from Func, and merging them with experience I learned from
working at Puppet Labs.  I wanted something that was easy to pick up and was installable
without any bootstrapping, and didn't suffer from the "I don't want to learn X" mentality
that often impacted adoption of tools like Puppet and Chef among certain ops teams.

I also spent some time working with a couple of sites that needed to do large webapp deployments, 
and noticed how complex various configuration management and deployment tools were to these
companies, compared with what they actually needed.  Release processes were too complex
and needed something simple to straighten them out -- but I really didn't want to train
all the dev(ops) on Puppet or Chef, and they really didn't want to learn them either.

I kept thinking, is there a  reason for these programs to be so large and complicated?  
Well, systems management is a little complicated, but no.  Not really.   

Can I build something that a sysadmin can 
figure out in 15 minutes and get going, and then extend in any language he knows?  
That's how Ansible was born.  It sheds 'best practices' for 'you know your infrastructure
best', and distills all of the ideas behind all of these other tools to the core.

Not only is Ansible very simple and easy to learn/extend, it's configuration management, deployment, and ad-hoc tasks all in one app.  And I think that makes it pretty powerful.  It hasn't really been done before.

I'd like to know what you think of it.  Hop by the mailing list and say hi.

Comparisons
-----------

vs Func?
++++++++

Ansible uses SSH by default instead of SSL and custom daemons, and requires
no extra software to run on managed machines.  You can also write modules
in any language as long as they return JSON.  Ansible's API, of course, is
heavily inspired by Func.   Some features, like delegation hierarchies, are
not supported, but Ansible does have an async mode.  Ansible also adds
a configuration management and multinode orchestration layer (:doc:`playbooks`) 
that Func didn't have.

vs Puppet?
++++++++++

First off, Ansible wouldn't have happened without Puppet.  Puppet took configuration
management ideas from cfengine and made them sane.  However, I still think they can
be simpler.

Ansible playbooks ARE a complete configuration management system.  Unlike Puppet, playbooks
are implicitly ordered (more like Chef), but still retain the ability to signal
notification events (like Puppet).  This is kind of a 'best of both worlds' thing.

There is no central server to promote scaling, and Ansible is 
also designed with multi-node deployment in mind from day-one -- something that is difficult
for Puppet because of the pull architecture.  Ansible is push based,
so you can do things in an ordered fashion, addressing batches of servers
at one time, and you do not have to contend with the DAG.  It's also extensible in any language
and the source is designed so that you don't have to be an expert programmer to submit a patch.

Ansible's resources are heavily inspired by Puppet, with the "state" keyword being a more or less
direct port of "ensure" from Puppet.  Unlike Puppet, Ansible can be extended in any language,
even bash ... just return some output in JSON format.  You don't need to know Ruby.

Unlike Puppet, hosts are taken out of playbooks when they have a failure.  It encourages
'fail first', so you can correct the error, instead of configuring as much of the system
as it can.  A system shouldn't be half correct, especially if we're planning on configuring
other systems that depend on that system.

Ansible also has a VERY short learning curve -- but it also has less language constructs and
does not create its own programming language.   What constructs Ansible does have should be enough to cover 80% or so of the cases of most Puppet users, and it should scale equally well (not having a server is
almost like cheating).

I also suspect some Ansible users will actually use Ansible to trigger Puppet -- using the git
module to checkout a Puppet module hierachy from source, and the command module to run
'puppet apply'.  That's ok too, but you may find playbooks do all you need.

Ansible does support gathering variables from 'facter', if installed, and Ansible templates
in jinja2 in a way just like Puppet does with erb.

vs Chef?
++++++++

Much in the ways Ansible is different from Puppet.  Chef is notoriously hard
to set up on the server, and requires that you know how to program in Ruby to
use the language.  As such, it seems to have a pretty good following mainly
among Rails coders.

Like Chef (and unlike Puppet), Ansible executes configuration tasks in the order
given, rather than having to manually specify a dependency graph.  Ansible extends
this though, by allowing triggered notifiers, so Apache can, be restarted if needed,
only once, at the end of a configuration run.

Unlike Chef, Ansible's playbooks are not a programming language.   This means
that you can parse Ansible's playbooks and treat the instructions as data.  It also
means working on your infrastructure is not a development task and testing is easier.

Ansible can be used regardless of your programming language experience.  Both
Chef and Puppet are around 60k+ lines of code, while Ansible is a much simpler
program.  I believe this strongly leads to more reliable software and a richer
open source community -- the code is kept simple so it is easy for anyone to
submit a patch or module.

Just like with puppet, some users may wish to use Ansible to trigger chef-solo to
avoid using the server, after checking out some chef content using Ansible's git
support.

Ansible does support gathering variables from 'ohai', if installed.

vs Capistrano/Fabric?
+++++++++++++++++++++

These tools aren't really well suited to doing idempotent configuration and are
typically about pushing software out for web deployment and automating steps.  

Meanwhile Ansible is designed for other types of configuration management, and contains some
advanced scaling features.  

The ansible playbook syntax is documented within a page of text and also has a MUCH lower learning curve.  And because Ansible is designed for more than pushing webapps, it's more generally 
useful for sysadmins (not just web developers), and can also be used for firing off ad-hoc tasks.

Other Questions
---------------

How does Ansible scale?
+++++++++++++++++++++++

Whether in single-execution mode or using ansible playbooks, ansible can
run multiple commands in seperate forks, thanks to the magic behind
Python's multiprocessing module.  

If you need to address 500 machines you can decide if you want to try 
to contact 5 at a time, or 50 at a time.
It's up to you and how much power you can throw at it, but its heritage
is about handling those kinds of use cases.   

There are no daemons so it's entirely up to you.  When you are aren't using
Ansible, it is not consuming any resources.

If you have 10,000 systems, running a single ansible playbook against all of
them probably isn't always appropriate, but most users shouldn't have any problems.
If you want to kick off an async task/module, it's probably fine.

If you'd like to discuss scaling, please hop on the mailing list.

Are transports other than SSH supported?
++++++++++++++++++++++++++++++++++++++++

Currently SSH is the only transport, though the interface is pluggable so a 
small patch could bring transport over message bus or XMPP as an option.
Stop by the mailing list if you have ideas.

What are some ideal uses for Ansible?
+++++++++++++++++++++++++++++++++++++

One of the best use cases? Complex multi-node cloud deployments using playbooks.  Another good
example is for configuration management where you 
are starting from a clean OS with no extra software installed, adopting systems
that are already deployed. 

Ansible is also great for running ad-hoc tasks across a wide variety of Linux, Unix, and BSDs.  
Because it just uses the basic tools available on the system, it is exceptionally cross platform
without needing to install management packages on each node.

It also excels for writing distributed
scripts and ad-hoc applications that need to gather data or perform arbitrary
tasks -- whether for a QA sytem, build system, or anything you can think of.

.. seealso::

   :doc:`examples`
       Examples of basic commands
   :doc:`playbooks`
       Learning ansible's configuration management language
   `Mailing List <http://groups.google.com/group/ansible-project>`_
       Questions? Help? Ideas?  Stop by the list on Google Groups
   `irc.freenode.net <http://irc.freenode.net>`_
       #ansible IRC chat channel

