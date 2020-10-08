[![Build Status](https://travis-ci.org/mkayontour/ansible-icingaweb2.svg?branch=master)](https://travis-ci.org/mkayontour/ansible-icingaweb2)
[![volkswagen status](https://auchenberg.github.io/volkswagen/volkswargen_ci.svg?v=1)](https://github.com/auchenberg/volkswagen)
[![Ansible Quality Score](https://img.shields.io/ansible/quality/50067?label=role%20quality)](https://galaxy.ansible.com/mkayontour/icingaweb2)


ansible-icingaweb2
=========

A Ansible role to install Icinga Web 2 and configures it throughly. The roles contains installation of Icinga Web 2 packages, configuration of the all configuration files and setup of the monitoring module.

In addition the role provides an easy way to install and enable Icinga Web 2 modules. The configuration of those modules need to be done manually or in a seperate role.

Installation
------------

```
ansible-galaxy install mkayontour.icingaweb2
```

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

### Installation

The manage variables always trigger something to be managed.
Package is the normal icingaweb2 packages and repository is the icinga release repo.
```
icingaweb2_manage_package: yes
icingaweb2_manage_centos_scl: yes
icingaweb2_manage_repository: yes
```

If the Director should be managed, you can use the following variable.
```
icingaweb2_manage_director: yes
```

For more Information about the Director see section `director` down below.


### Icinga Web 2 Configuration

**resources.ini**

The resources of Icinga Web 2 are defined at `icingaweb2_resources` dictionary.
Key is name of the resource and then all needed options which go into the **resources.ini**
```
icingaweb2_resources:
  icinga_ido:
    type: db
    db: mysql
    host: localhost
    dbname: icinga
    username: icinga
    password: icinga
    use_ssl: 0
    charset: utf8
  icingaweb_db:
    type: db
    db: mysql
    host: localhost
    dbname: icingaweb
    username: icingaweb
    password: icingaweb
    use_ssl: 0
```

All Icinga Web 2 configuration files have their own dictionary to create every section and option.

**groups.ini**
```
icingaweb2_groups:
  icingaweb2:
    backend: db
    resource: icingaweb_db
```

**authentication.ini**
```
icingaweb2_authentication:
  icingaweb2:
    backend: db
    resource: icingaweb_db
```

**config.ini**
```
icingaweb2_config:
  global:
    show_stacktraces: 1
    show_application_state_messages: 1
    config_backend: ini
    module_path: /usr/share/icingaweb2/modules
  logging:
    log: syslog
    level: ERROR
    application: icingaweb2
    facility: user
  themes:
    default: Icinga
```

If you want to congfigure your roles automatically then please use this dictionary. When the var **icingaweb2_roles** is not defined, the task will be skipped.
```
icingaweb2_roles:
  administrators:
    users: icinga
    permissions: "*"
    groups: Administrators
```

### Monitoring Module

The monitoring module will be installed by default. To create configuration for the module please use the following dictionaries.
```
icingaweb2_monitoring_config:
  security:
    protected_customvars: "*pw*,*pass*,community"
icingaweb2_monitoring_backends:
  icinga:
    type: ido
    resource: icinga_ido
icingaweb2_monitoring_commandtransports:
  icinga2:
    transport: api
    host: localhost
    port: 5665
    username: root
    password: root
```

### Adding Icinga Web 2 Modules

To add specific modules to Icinga Web 2 you can configure this list of modules.
Please ensure the monitoring module stays in this list as well.

All modules in this list with the option **git_url** set will be cloned via git to the modules directory.

**INFO: Ensure git is installed on the system before starting the playbook**

```
icingaweb2_modules:
  - name: monitoring
  # - name: director
  #   git_url: https://github.com/Icinga/icingaweb2-module-director.git
  #   version: v1.7.2
  # - name: ipl
  #   git_url: https://github.com/Icinga/icingaweb2-module-ipl.git
  #   version: v0.5.0
  # - name: reactbundle
  #   git_url: https://github.com/Icinga/icingaweb2-module-reactbundle.git
  #   version: v0.7.0
  # - name: incubator
  #   git_url: https://github.com/Icinga/icingaweb2-module-incubator.git
  #   version: v0.5.0
```

### Director
**Please make sure the git repo for the director is installed or use the feature
to add icingaweb2 modules.**

To manage the director, you can use the variable `icingaweb2_manage_director` to
enable the include.

To enable the systemd service use the variable `icingaweb2_manage_director_service`.

Then there are a few variables to configure the **config.ini** and **kickstart.ini**.

**config.ini**

`icingaweb2_director_resource`: Name of the Icinga Web 2 Director resource (DB)


**kickstart.ini**


`icingaweb2_director_kickstart_endpoint:` For the kickstart script the name of the endpoint (common name of the master). If this var is not defined, the **kickstart.ini** won't be created. And therefore the kickstart won't be run.

`icingaweb2_director_kickstart_host:` can be a resolvable hostname or an IP address.
Default: "localhost"

`icingaweb2_director_kickstart_port:` Port is 5665 per default in case none is given.

`icingaweb2_director_kickstart_username:` Username of the Director API User. Default: "root"

`icingaweb2_director_kickstart_password:` Password of the Director API User. Default: "root"


Dependencies
------------

There are no dependencies for this module.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: icingaweb2
      roles:
         - { role: mkayontour.icingaweb2 }

License
-------

Apache-2.0

Author Information
------------------

Twitter: @mkayontour
