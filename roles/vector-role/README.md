Vector
=========

This role can install and configure Vector on Centos

Role Variables
--------------

| vars                  | description                  |
|-----------------------|------------------------------|
| vector_version        | version to install           |
| vector_clickhouse_ip  | clickhouse ip address        |
| clickhouse_db_name    | clickhouse db for store logs |
| clickhouse_table_name | clickhouse db to write logs  |
| vector_url            | url for download             |
| vector_config_dir     | location for config file     |
| vector_config         | config file                  |


Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```
- name: Install Vector
  hosts: vector
  remote_user: ana
  roles:
     - vector-role
```

License
-------

MIT

Author Information
------------------

Anastasiya Sukhodola
