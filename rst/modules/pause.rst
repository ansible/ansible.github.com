.. _pause:

pause
`````

.. versionadded:: 0.8

Pauses playbook execution for a set amount of time, or until a prompt
is acknowledged. All parameters are optional. The **default behavior**
is to pause with a prompt. The :ref:`examples<pauseexamples>` describe
some general use cases.

+---------------+----------+---------+---------------------------------------------+
| parameter     | required | default | comments                                    |
+===============+==========+=========+=============================================+
| minutes       | no       |         | number of minutes to pause for              |
+---------------+----------+---------+---------------------------------------------+
| seconds       | no       |         | number of seconds to pause for              |
+---------------+----------+---------+---------------------------------------------+
| prompt        | no       |         | optional text to use for the prompt message |
+---------------+----------+---------+---------------------------------------------+

.. tip::
   You can use ``ctrl+c`` if you wish to advance a pause earlier than
   it is set to expire **or** if you need to abort a playbook run
   entirely. **To continue early:** press ``ctrl+c`` and then
   ``c``. **To abort a playbook:** press ``ctrl+c`` twice.

The pause module integrates into async/parallelized playbooks without
any special considerations (see also: :ref:`rolling-updates`). When
using *prompted* pauses each host must be advanced
individually. *Timed* pauses advance as soon as they expire.

.. _pauseexamples:

Examples from :doc:`playbooks`::

    ---
    - hosts: webservers
      tasks:
        # Build up a cache before putting into rotation.
        - name: pause for 5 minutes to build app cache
          action: pause minutes=5

        # In a production environment you may wish to pause until you
        # can verify updates to an application were successful.
        - name: verify app updates before continuing
          action: pause

        # A helpful reminder of what to look out for post-update.
        - name: verify app updates before continuing
          action: pause prompt=Make sure org.foo.FooOverload exception is not present
