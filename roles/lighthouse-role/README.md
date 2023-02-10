Lighthouse
=========

This role can install and configure Lighthouse on Centos, it includes nginx install

Requirements
------------

Part of role is to install and configure NGINX (or another web server) before run this role.

Role Variables
--------------

| vars                  | description                        |
|-----------------------|------------------------------------|
| lighhouse_location_dir        | location to store lighthouse files |
|lighthouse_url | Clickhouse repo url                | 


Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```
- name: Install Lighthouse
  hosts: lighthouse
  remote_user: ana
  roles:
    - lighthouse-role
```

License
-------

MIT

Author Information
------------------

Anastasiya Sukhodola
