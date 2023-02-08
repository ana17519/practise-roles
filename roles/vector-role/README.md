Vector
=========

This role can install Vector

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

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: clickhouse }

License
-------

MIT

Author Information
------------------

Anastasiya Sukhodola
