# Ansible Role: mysql

Installs, configures and initializes MySQL server on Ubuntu server 18.04.

## Requirements

The role is pretty much self-contained, only requires root access.
Either config ansible `become=True` globally, or call this role in your playbook like:

    - hosts: db-servers
      roles:
        - role: mysql
          become: yes

## Variables

Available variables are listed below, along with default values (see `defaults/main.yml` for all available variables):

    mysql_datadir: /var/lib/mysql

MySQL data directory. Data directory will be created and set owner:group to `mysql:mysql`.

    mysql_root_home: /root

The home directory of MySQL root user. Root user's credential will be wrote to `.my.cn` in that directory
for later database and/or user configuration.

    mysql_root_username: root

The MySQL root user account. Would always be root on Ubuntu server.

    mysql_root_password: root

This role would change MySQL root user's password after first configuration.

> Note: If you get an error like `ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)` after a failed or interrupted playbook run, this usually means the root password wasn't updated correctly after first configuration. Remove the `.my.cnf` file inside the `mysql_root_home` directory if there is one, and empty `mysql_datadir`, then run the playbook again.

    mysql_config_file: /etc/mysql/my.cnf
    mysql_config_include_dir: /etc/mysql/conf.d

The global my.cnf configuration file and include directory. The default values are tuned for a server where MySQL can consume up to 1 GB RAM.

    mysql_databases: []

The MySQL databases to manage. A database has the values:
  - `name`
  - `encoding` (defaults to `utf8`)
  - `collation` (defaults to `utf8_general_ci`)
  - `state` (defaults to `present`)

Arguments have the same format as those in the `mysql_db` module.

    mysql_users: []

The MySQL (non-root) users to manage. A user has the values:

  - `name`
  - `host` (defaults to `localhost`)
  - `password`
  - `priv` (defaults to `*.*:USAGE`)
  - `state`  (defaults to `present`)

Arguments have the same format as those in the `mysql_db` module.

    mysql_packages:
      - mysql-client
      - mysql-server

MySQL packages to be installed. You might want to add additional packages or substitute some of them.

    mysql_port: 3306
    mysql_bind_address: "0.0.0.0"
    mysql_pid_file: /var/run/mysqld/mysqld.pid
    mysql_socket: /var/run/mysqld/mysqld.sock

Default MySQL connection configuration.

    mysql_log_dir: /var/log/mysql
    mysql_slow_query_log_file: /var/log/mysql/mysql-slow.log
    mysql_log_error: /var/log/mysql/error.log

MySQL logging configuration.

> NOTE: The data directory, pid file, socket, and log files associated with mysqld are protected by AppArmor (the Ubuntu equivalent of SELinux), this role will manage those rules for you.

## Dependencies

None.

## Example Playbook

    - hosts: db-serverss
      become: yes
      vars_files:
        - vars/main.yml
      roles:
        - { role: mysql }

*Inside `vars/main.yml`*:

    mysql_datadir: /data/mysql
    mysql_root_password: top-secret
    mysql_databases:
      - name: some_database
        encoding: utf8mb4
        collation: utf8mb4_general_ci
    mysql_users:
      - name: some_user
        host: "localhost"
        password: another-top-secret
        priv: "some_database.*:ALL"

## License

MIT / BSD
