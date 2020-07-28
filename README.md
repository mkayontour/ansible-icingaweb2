[![Build Status](https://travis-ci.org/mkayontour/ansible-icingaweb2.svg?branch=master)](https://travis-ci.org/mkayontour/ansible-icingaweb2)

ansible-icingaweb2
=========

A Ansible role to install Icinga Web 2 and configures it throughly. The roles contains installation of Icinga Web 2 packages, configuration of the all configuration files and setup of the monitoring module.

In addition the role provides an easy way to install and enable Icinga Web 2 modules. The configuration of those modules need to be done manually or in a seperate role.

Requirements
------------

There are no specific requirements needed for this role. But a few this need to be mentioned ahead.

Choose your webserver application by your self, by installing the webserver before installing Icinga Web 2.

In addition you need a role which provides a running database and please make sure to import the schema at first. If you want to use authentication within the database. If you are using ldap or external auth there's no need to have the database.

```
- name: Import Icinga Web 2 database schema.
  mysql_db:
    name: icingaweb
    state: import
    target: /usr/share/icingaweb2/etc/schema/mysql.schema.sql
    login_user: icingaweb
    login_password: icingaweb
    login_host: localhost

```



Role Variables
--------------

A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
