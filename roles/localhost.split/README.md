Role Name
=========

Demo of using split with an Ansible variable.

Requirements
------------

- The directory `./working`.

Role Variables
--------------

Variables are read from `host_vars/localhost/`

Dependencies
------------

None.

Example Playbook
----------------

See `snippets.yml`.  Running just the `localhost.split` role:
```bash
ansible-playbook -i localhost snippets.yml -t split
```

License
-------

BSD

Author Information
------------------
