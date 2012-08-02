.. highlight:: yaml

YAML Syntax
===========

This page provides a basic overview of correct YAML syntax, which is how Ansible
playbooks (our configuration management language) are expressed.

We use YAML because it is easier to read and write for humans than other common
data formats like XML or JSON.  Further, there are libraries available for reading
and writing YAML in most programming languages.

You may also wish to read :doc:`playbooks` at the same time to see how this
is used in practice.


YAML Basics
-----------

For Ansible, nearly every YAML file starts with a list.  Each item in
the list is a list of key/value pairs, commonly called a *hash* or a
*dictionary*.  So, we need to know how to write lists and dictionaries
in YAML.

There's another small quirk to YAML.  All YAML files (regardless of
their association with Ansible or not) should start with ``---``.
This is just a YAML format thing that means "this is the start of a
document".

All members of a list are lines beginning at the same indentation level starting
with a ``-`` (dash) character::

    ---
    # A list of tasty fruits
    - Apple
    - Orange
    - Strawberry
    - Mango

A dictionary is represented in a simple ``key:`` and ``value`` form::

    ---
    # An employee record
    name: John Eckersberg
    job: Developer
    skill: Elite

Dictionaries can also be represented in an abbreviated form if you really want to::

    ---
    # An employee record
    {name: John Eckersberg, job: Developer, skill: Elite}

.. _truthiness:

Ansible doesn't really use these too much, but you can also specify a
boolean value (true/false) in several forms::

    ---
    knows_oop: True
    likes_emacs: TRUE
    uses_cvs: false

Let's combine what we learned so far in an arbitary YAML example.  This really
has nothing to do with Ansible, but will give you a feel for the format::

    ---
    # An employee record
    name: John Eckersberg
    job: Developer
    skill: Elite
    employed: True
    foods:
        - Apple
        - Orange
        - Strawberry
        - Mango
    languages:
        ruby: Elite
	python: Elite
	dotnet: Lame

That's all you really need to know about YAML to get started writing
Ansible :doc:`playbooks`.

.. seealso::

   :doc:`playbooks`
       Learn what playbooks can do and how to write/run them.
   `YAMLLint <http://yamllint.com/>`_
       YAML Lint (online) helps you debug YAML syntax if you are having problems
   `Github examples directory <https://github.com/ansible/ansible/tree/master/examples/playbooks>`_
       Complete playbook files from the github project source
   `Mailing List <http://groups.google.com/group/ansible-project>`_
       Questions? Help? Ideas?  Stop by the list on Google Groups
   `irc.freenode.net <http://irc.freenode.net>`_
       #ansible IRC chat channel
