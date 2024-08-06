<!-- BEGIN_ANSIBLE_DOCS -->

# Ansible Role: trippsc2.postgresql.install
Version: 1.0.5

This role installs and does initial configuration for PostgreSQL on Linux machines.

## Requirements

| Platform | Versions |
| -------- | -------- |
| Debian | <ul><li>bullseye</li><li>bookworm</li></ul> |
| EL | <ul><li>8</li><li>9</li></ul> |
| Ubuntu | <ul><li>focal</li><li>jammy</li><li>noble</li></ul> |

## Dependencies

| Collection |
| ---------- |
| ansible.posix |
| community.general |
| community.hashi_vault |
| community.postgresql |
| trippsc2.hashi_vault |

## Role Arguments
|Option|Description|Type|Required|Choices|Default|
|---|---|---|---|---|---|
| pgsql_configure_logrotate | <p>Whether to configure log rotation for the PostgreSQL log files.</p> | bool | no |  | true |
| pgsql_configure_firewall | <p>Whether to configure the host firewall for use with PostgreSQL server.</p><p>If *pgsql_listen_on_local_only* is `true`, this defaults to `false`.</p> | bool | no |  | false |
| pgsql_configure_monitoring | <p>Whether to configure monitoring for PostgreSQL.</p><p>If *pgsql_listen_on_local_only* is `true`, this defaults to `false`.</p> | bool | no |  | true |
| pgsql_configure_vault_database_connection | <p>Whether to configure a HashiCorp Vault database secret engine to manage PostgreSQL credentials.</p> | bool | no |  | true |
| pgsql_major_version | <p>The major version of PostgreSQL to install.</p> | int | no | <ul><li>13</li><li>14</li><li>15</li><li>16</li></ul> | 16 |
| pgsql_install_pgaudit | <p>Whether to install the PGAudit extension.</p> | bool | no |  | true |
| pgsql_install_timescaledb | <p>Whether to install the TimescaleDB extension.</p> | bool | no |  | false |
| pgsql_timezone | <p>The timezone to use for the PostgreSQL server.</p><p>See https://en.wikipedia.org/wiki/List_of_tz_database_time_zones for a list of valid timezones.</p> | str | no |  | America/New_York |
| pgsql_vault_managed_monitoring_credentials | <p>Whether to manage monitoring credentials in HashiCorp Vault.</p><p>If *pgsql_configure_monitoring* is `false`, this option is ignored.</p> | bool | no |  | false |
| pgsql_create_vault_secret_engines | <p>Whether to create the Vault database mount point.</p> | bool | no |  | true |
| pgsql_vault_database_mount_point | <p>The mount point for the HashiCorp Vault database secret engine.</p><p>This is only used if *pgsql_configure_vault_database_connection* is `true`.</p> | str | no |  | database |
| pgsql_vault_user | <p>The username for the PostgreSQL user in HashiCorp Vault.</p><p>This is only used if *pgsql_configure_vault_database_connection* is `true`.</p> | str | no |  | vault |
| pgsql_vault_access_hostname | <p>The FQDN or IP address for Vault to use when connecting to the PostgreSQL server.</p><p>This is only used if *pgsql_configure_vault_database_connection* is `true`.</p> | str | no |  |  |
| pgsql_vault_ssl_mode | <p>The SSL mode to use when connecting to the PostgreSQL database.</p><p>See https://www.postgresql.org/docs/current/libpq-ssl.html#LIBPQ-SSL-PROTECTION for more information.</p><p>This is only used if *pgsql_configure_vault_database_connection* is `true`.</p> | str | no | <ul><li>disable</li><li>require</li><li>verify-ca</li><li>verify-full</li></ul> | verify-full |
| pgsql_vault_monitor_mount_point | <p>The KV version 2 mount point for the HashiCorp Vault monitoring secret.</p><p>This is only used if *pgsql_configure_monitoring* and *pgsql_vault_managed_monitoring_credentials* are `true`.</p> | str | no |  | secret |
| pgsql_vault_monitor_path | <p>The path to the monitoring credentials in HashiCorp Vault.</p><p>This is only used if *pgsql_configure_monitoring* and *pgsql_vault_managed_monitoring_credentials* are `true`.</p> | str | no |  | pgsql/{{ inventory_hostname }} |
| pgsql_monitoring_user | <p>The username for the monitoring user.</p><p>This is only used if *pgsql_configure_monitoring* is `true`.</p> | str | no |  | zbx_monitor |
| pgsql_monitoring_password | <p>The password for the monitoring user.</p><p>This is only used if *pgsql_configure_monitoring* is `true` and *pgsql_vault_managed_monitoring_credentials* is `false`.</p> | str | no |  |  |
| pgsql_listen_on_local_only | <p>Whether the PostgreSQL server should only listen on the loopback address.</p><p>If this is `false`, *pgsql_configure_firewalld* and *pgsql_configure_monitoring* default to `true`.</p><p>This is mutually exclusive with *pgsql_listen_on_all_addresses*.</p> | bool | no |  | false |
| pgsql_listen_on_all_addresses | <p>Whether the PostgreSQL server should listen on all available network interfaces.</p><p>This is mutually exclusive with *pgsql_listen_on_local_only*.</p> | bool | no |  | true |
| pgsql_listen_addresses | <p>A list of IP addresses on which the PostgreSQL server should listen.</p><p>This is required if *pgsql_listen_on_all_addresses* and *pgsql_listen_on_local_only* are `false`. Otherwise, it is ignored.</p> | list of 'str' | no |  |  |
| pgsql_port | <p>The port on which the PostgreSQL server should listen.</p> | int | no |  | 5432 |
| pgsql_password_encryption | <p>The method to use for password encryption.</p> | str | no | <ul><li>scram-sha-256</li><li>md5</li></ul> | scram-sha-256 |
| pgsql_ssl | <p>Whether to enable SSL for the PostgreSQL server.</p> | bool | no |  | false |
| pgsql_ssl_ca_file | <p>The path to the SSL certificate authority file.</p><p>This is only used if *pgsql_ssl* is `true`.</p><p>Relative paths are relative to the PostgreSQL data directory.</p> | str | no |  |  |
| pgsql_ssl_cert_file | <p>The path to the SSL certificate file.</p><p>This is only used if *pgsql_ssl* is `true`.</p><p>Relative paths are relative to the PostgreSQL data directory.</p> | str | no |  | server.crt |
| pgsql_ssl_key_file | <p>The path to the SSL key file.</p><p>This is only used if *pgsql_ssl* is `true`.</p><p>Relative paths are relative to the PostgreSQL data directory.</p> | str | no |  | server.key |
| pgsql_max_connections | <p>The maximum number of client connections allowed.</p> | int | no |  | 100 |
| pgsql_shared_buffers | <p>The amount of memory allocated to shared memory buffers.</p> | str | no |  | 128MB |
| pgsql_work_mem | <p>The amount of memory allocated to query operations.</p> | str | no |  | 4MB |
| pgsql_maintenance_work_mem | <p>The amount of memory allocated to maintenance operations.</p> | str | no |  | 64MB |
| pgsql_effective_cache_size | <p>The amount of disk cache available to a single query.</p> | str | no |  | 4GB |
| pgsql_min_wal_size | <p>The minimum size of the write-ahead log.</p> | str | no |  | 80MB |
| pgsql_max_wal_size | <p>The maximum size of the write-ahead log.</p> | str | no |  | 1GB |
| pgsql_effective_io_concurrency | <p>The number of concurrent disk I/O operations.</p> | int | no |  | 1 |
| pgsql_max_worker_processes | <p>The maximum number of background worker processes.</p> | int | no |  | 8 |
| pgsql_max_parallel_workers_per_gather | <p>The maximum number of parallel workers per gather node.</p> | int | no |  | 2 |
| pgsql_max_parallel_workers | <p>The maximum number of parallel workers.</p> | int | no |  | 8 |
| pgsql_max_parallel_maintenance_workers | <p>The maximum number of parallel maintenance workers.</p> | int | no |  | 2 |
| pgsql_checkpoint_completion_target | <p>The target completion time for checkpoints.</p> | float | no |  | 0.9 |
| pgsql_random_page_cost | <p>The estimated cost of a non-sequentially fetched disk page.</p> | float | no |  | 4.0 |
| pgsql_default_statistics_target | <p>The number of most common values to store in a table's statistics.</p> | int | no |  | 100 |
| pgsql_log_to_stderr | <p>Whether to log to stderr.</p> | bool | no |  | true |
| pgsql_log_to_csvlog | <p>Whether to log to a CSV file.</p> | bool | no |  | false |
| pgsql_log_to_syslog | <p>Whether to log to syslog.</p> | bool | no |  | false |
| pgsql_logging_collector | <p>Whether logs from stderr and CSV files should be written to file.</p><p>If *pgsql_log_to_stderr* and *pgsql_log_to_csvlog* are `false`, this is ignored.</p> | bool | no |  | true |
| pgsql_log_directory | <p>The directory in which log files should be stored.</p><p>This is only used if *pgsql_logging_collector* is `true`.</p> | path | no |  | /var/log/postgres |
| pgsql_log_filename | <p>The name of the log file.</p><p>This is only used if *pgsql_logging_collector* is `true`.</p> | str | no |  | postgresql.log |
| pgsql_log_file_mode | <p>The file mode for the log file.</p><p>This is only used if *pgsql_logging_collector* is `true`.</p> | str | no |  | 0600 |
| pgsql_log_rotation_age | <p>The maximum age of a log file before it is rotated by PostgreSQL.</p><p>If log rotation is managed by logrotate, this should be set to `0`.</p><p>This is only used if *pgsql_logging_collector* is `true`.</p> | str | no |  | {{ '0' if pgsql_configure_logrotate else '1d' }} |
| pgsql_log_rotation_size | <p>The maximum size of a log file before it is rotated by PostgreSQL.</p><p>If log rotation is managed by logrotate, this should be set to `0`.</p><p>This is only used if *pgsql_logging_collector* is `true`.</p> | str | no |  | {{ '0' if pgsql_configure_logrotate else '10MB' }} |
| pgsql_log_truncate_on_rotation | <p>Whether to truncate the log file on rotation.</p><p>This is only used if *pgsql_logging_collector* is `true`.</p> | bool | no |  | true |
| pgsql_syslog_facility | <p>The syslog facility to use for logging.</p><p>This is only used if *pgsql_log_to_syslog* is `true`.</p> | str | no |  | LOCAL0 |
| pgsql_syslog_ident | <p>The syslog identifier to use for logging.</p><p>This is only used if *pgsql_log_to_syslog* is `true`.</p> | str | no |  | postgres |
| pgsql_syslog_sequence_numbers | <p>Whether to add sequence numbers to syslog messages to prevent summarizing.</p><p>This is only used if *pgsql_log_to_syslog* is `true`.</p> | bool | no |  | true |
| pgsql_syslog_split_messages | <p>Whether to split syslog messages into multiple lines.</p><p>This is only used if *pgsql_log_to_syslog* is `true`.</p> | bool | no |  | true |
| pgsql_log_min_messages | <p>The minimum message severity to log.</p> | str | no | <ul><li>DEBUG5</li><li>DEBUG4</li><li>DEBUG3</li><li>DEBUG2</li><li>DEBUG1</li><li>LOG</li><li>NOTICE</li><li>WARNING</li><li>ERROR</li><li>FATAL</li><li>PANIC</li></ul> | WARNING |
| pgsql_log_min_error_statement | <p>The minimum error severity to log.</p> | str | no | <ul><li>DEBUG5</li><li>DEBUG4</li><li>DEBUG3</li><li>DEBUG2</li><li>DEBUG1</li><li>LOG</li><li>NOTICE</li><li>WARNING</li><li>ERROR</li><li>FATAL</li><li>PANIC</li></ul> | ERROR |
| pgsql_debug_print_parse | <p>Whether to print the parse tree of each query.</p> | bool | no |  | false |
| pgsql_debug_print_rewritten | <p>Whether to print the query rewriter output.</p> | bool | no |  | false |
| pgsql_debug_print_plan | <p>Whether to print the execution plan for each query.</p> | bool | no |  | false |
| pgsql_debug_pretty_print | <p>Whether to pretty-print the query.</p> | bool | no |  | true |
| pgsql_log_connections | <p>Whether to log connections.</p> | bool | no |  | true |
| pgsql_log_disconnections | <p>Whether to log disconnections.</p> | bool | no |  | true |
| pgsql_log_error_verbosity | <p>The verbosity of error messages.</p> | str | no | <ul><li>TERSE</li><li>DEFAULT</li><li>VERBOSE</li></ul> | VERBOSE |
| pgsql_log_line_prefix | <p>The prefix to add to each log line.</p> | str | no |  | %m [%p]: [%l-1] db=%d,user=%u,app=%a,client=%h |
| pgsql_log_statement | <p>The type of statements to log.</p><p>See https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT for the types of statements logged for each level.</p> | str | no | <ul><li>none</li><li>ddl</li><li>mod</li><li>all</li></ul> | ddl |
| pgsql_log_timezone | <p>The timezone to use for timestamps in the log file.</p> | str | no |  | {{ pgsql_timezone }} |
| pgsql_pgaudit_log | <p>The types of events to log with PGAudit.</p><p>See https://github.com/pgaudit/pgaudit/blob/master/README.md#pgauditlog for more information.</p> | list of 'str' | no | <ul><li>READ</li><li>WRITE</li><li>FUNCTION</li><li>ROLE</li><li>DDL</li><li>MISC</li><li>MISC_SET</li><li>ALL</li></ul> | ["DDL", "WRITE"] |
| pgsql_additional_shared_preload_libraries | <p>A list of additional shared libraries to preload.</p><p>This is not including PGAudit or TimescaleDB, if *pgsql_install_pgaudit* or *pgsql_install_timescaledb* are `true` respectively.</p> | list of 'str' | no |  |  |
| pgsql_vault_ip_addresses | <p>A list of IP addresses from which HashiCorp Vault will access the PostgreSQL server.</p><p>This is only used if *pgsql_configure_vault_database_connection* is `true`.</p><p>This should not include the loopback address.</p> | list of 'str' | no |  |  |
| pgsql_monitoring_ip_addresses | <p>A list of IP addresses from which monitoring services will access the PostgreSQL server.</p><p>This is only used if *pgsql_configure_monitoring* is `true`.</p><p>This should not include the loopback address.</p> | list of 'str' | no |  |  |
| pgsql_additional_hba_entries | <p>A list of additional host-based authentication entries.</p><p>This should include any external services that need to access the PostgreSQL server.</p> | list of dicts of 'pgsql_additional_hba_entries' options | no |  |  |
| pgsql_firewall_type | <p>The type of host firewall to configure.</p><p>For Debian and EL systems, this defaults to `firewalld`.</p><p>For Ubuntu systems, this defaults to `ufw`.</p> | str | no | <ul><li>firewalld</li><li>ufw</li></ul> |  |
| pgsql_databases | <p>A list of databases to create.</p> | list of dicts of 'pgsql_databases' options | no |  |  |
| pgsql_users | <p>A list of users to create.</p> | list of dicts of 'pgsql_users' options | no |  |  |

### Options for pgsql_additional_hba_entries
|Option|Description|Type|Required|Choices|Default|
|---|---|---|---|---|---|
| type | <p>The type of host-based authentication entry.</p><p>See https://www.postgresql.org/docs/current/auth-pg-hba-conf.html#AUTH-PG-HBA-CONF for more information.</p> | str | yes | <ul><li>local</li><li>host</li><li>hostssl</li><li>hostnossl</li><li>hostgssenc</li><li>hostnogssenc</li></ul> |  |
| database | <p>The name of the database to which the entry applies.</p><p>This can be `all` to apply to all databases.</p> | str | yes |  |  |
| user | <p>The name of the user to which the entry applies.</p><p>This can be `all` to apply to all users.</p> | str | yes |  |  |
| address | <p>The CIDR range to which the entry applies.</p><p>This must not be provided for `local` entries.</p> | str | no |  |  |
| auth_method | <p>The authentication method to use.</p><p>See https://www.postgresql.org/docs/current/auth-pg-hba-conf.html#AUTH-PG-HBA-CONF for more information.</p> | str | yes | <ul><li>trust</li><li>reject</li><li>scram-sha-256</li><li>md5</li><li>password</li><li>gss</li><li>ident</li><li>peer</li><li>ldap</li><li>radius</li><li>cert</li><li>pam</li></ul> |  |
| auth_options | <p>Any additional options needed for the authentication method.</p><p>For most common authentication methods, this should not be provided.</p><p>See https://www.postgresql.org/docs/current/auth-pg-hba-conf.html#AUTH-PG-HBA-CONF for more information.</p> | str | no |  |  |

### Options for pgsql_databases
|Option|Description|Type|Required|Choices|Default|
|---|---|---|---|---|---|
| name | <p>The name of the database.</p> | str | yes |  |  |
| lc_collate | <p>The collation order for the database.</p> | str | no |  | en_US.UTF-8 |
| lc_ctype | <p>The character classification for the database.</p> | str | no |  | en_US.UTF-8 |
| encoding | <p>The character set encoding for the database.</p> | str | no |  | UTF-8 |
| template | <p>The template database to use for the new database.</p> | str | no |  | template0 |
| state | <p>The expected state of the database.</p> | str | no | <ul><li>present</li><li>absent</li></ul> | present |

### Options for pgsql_users
|Option|Description|Type|Required|Choices|Default|
|---|---|---|---|---|---|
| name | <p>The name of the user.</p> | str | yes |  |  |
| password | <p>The password for the user.</p><p>If not provided, the user will not have a password.</p><p>If Vault is managing the password, this should not be provided.</p> | str | no |  |  |
| role_attr_flags | <p>The options to add to the user during creation.</p><p>See https://www.postgresql.org/docs/current/sql-createrole.html for more information.</p> | list of 'str' | no |  |  |
| state | <p>The expected state of the user.</p> | str | no | <ul><li>present</li><li>absent</li></ul> | present |
| database_privileges | <p>A list of database privileges to grant to the user.</p> | list of dicts of 'database_privileges' options | no |  |  |

### Options for pgsql_users > database_privileges
|Option|Description|Type|Required|Choices|Default|
|---|---|---|---|---|---|
| database | <p>The name of the database to which the privilege applies.</p> | str | yes |  |  |
| privileges | <p>A list of privileges to grant to the user.</p><p>See https://www.postgresql.org/docs/current/sql-grant.html for more information.</p> | list of 'str' | yes |  |  |
| with_grant_option | <p>Whether to grant the user the ability to grant the privilege to others.</p> | bool | no |  | false |
| state | <p>The expected state of the privilege.</p> | str | no | <ul><li>present</li><li>absent</li></ul> | present |


## License
MIT

## Author and Project Information
Jim Tarpley
<!-- END_ANSIBLE_DOCS -->
