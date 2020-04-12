# Ansible role for Pretix

Ansible role for a pretix manual installation.

## Requirements

* Database: **PostgreSQL** _(recommended)_, MySQL (5.7 or newer) or MariaDB (10.2.7 or newer) or SQLite _(not recommended for production)_

## Role Variables

### Installation

| Variable Name | Function | Default value | Comment |
| ------------- | -------- | ------------- | ------- |
| `pretix_user` | User created for running the pretix service | pretix |
| `pretix_group` | Group for the user created for the pretix service | `{{ pretix_user }}` | 
| `pretix_version` | Version that is going to be installed _(required)_ |  |
| `pretix_systemd_service_web_name` | The name of the web systemd service file | `pretix-web` |
| `pretix_systemd_service_worker_name` | The name of the worker systemd service file | `pretix-worker` |
| `pretix_base_path` | Installation base path | `/opt/pretix` | Without a trailing slash
| `pretix_config_path` | Installation base path | `{{ pretix_base_path }}/config` | Without a trailing slash  
| `pretix_venv_path` | Python virtual environment path | `{{ pretix_base_path }}/.venv` | Should be a sub-directory of `pretix_base_path`; without a trailing slash
| `pretix_venv_command` | Command for creating the virtual environment | `python3 -m venv` |
| `pretix_bind_address` | Address gunicorn listens on _(required)_ |  | either socket or host:port

### Configuration File (pretix.cfg)
The configuration is built up, following https://docs.pretix.eu/en/latest/admin/config.html.
#### Block: Pretix
| Variable Name | Function | Default value | Comment |
| ------------- | -------- | ------------- | ------- |
| `pretix_pretix_instance_name` | Dynamic configuration directory path _(required)_ |  |
| `pretix_pretix_url` | The installation’s full URL _(required)_ | | Without a trailing slash
| `pretix_pretix_currency` | The default currency as a three-letter code | `EUR` |
| `pretix_pretix_datadir` | The local path to a data directory that will be used for storing user uploads and similar data | `{{ pretix_base_path }}/data` |
| `pretix_pretix_trust_x_forwarded_for` | Specifies whether the `X-Forwarded-For` header can be trusted (useful for reverse proxy) | `off` |
| `pretix_pretix_trust_x_forwarded_proto` | Specifies whether the `X-Forwarded-Proto` header can be trusted | `off` |
You can also add any additional config to the pretix block via the `pretix_pretix_additional_config` variable.

#### Block: Locale
| Variable Name | Function | Default value | Comment |
| ------------- | -------- | ------------- | ------- |
| `pretix_locale_default` | The system's default locale | `en` |
| `pretix_locale_timezone` | The system’s default timezone | `UTC` | as a `pytz` name

#### Block: Database
| Variable Name | Function | Default value | Comment |
| ------------- | -------- | ------------- | ------- |
| `pretix_database_backend` | The Database backend | | One of `postgresql` _(recommended)_, `mysql`, `oracle` or `sqlite3` _(not recommended for production)_
| `pretix_database_name` | The name of the database _(required)_ | |
| `pretix_database_user` | The user of the database _(required when not using sqlite3)_ | |
| `pretix_database_password` | The password of the user _(required when not using sqlite3)_ | |
| `pretix_database_host` | The host of the database _(required when not using sqlite3)_ | |
| `pretix_database_port` | The port used to connect to the database server _(required when not using sqlite3)_ | |
| `pretix_database_galera` | Indicates if the database backend is a MySQL/MariaDB Galera cluster | | Use only if backend is mysql. Options: `true/false`

#### Block: Mail
| Variable Name | Function | Default value | Comment |
| ------------- | -------- | ------------- | ------- |
| `pretix_mail_from` | The e-mail address set as `From` header in outgoing e-mails | `pretix@{{ ansible_fqdn }}` |
| `pretix_mail_host` | The host of the e-mail server | `localhost` |
| `pretix_mail_port` | The port used to connect to the mail server | `25` |
| `pretix_mail_user` | The user used to connect to the mail server | |
| `pretix_mail_password` | The password of the user | |
| `pretix_mail_ssl` | Use SSL/TLS for the connection to the mail server | `off` |
| `pretix_mail_tls` | Use STARTTLS for the connection to the mail server | `off` |
| `pretix_mail_admins` | List of E-Mail addresses that will be notified about every 500-error thrown by pretix | | comma-separated list

#### Block: Django
| Variable Name | Function | Default value | Comment |
| ------------- | -------- | ------------- | ------- |
| `pretix_django_secret` | Secret for Django _(recommended)_ | | Should be 50 character long string. If not provided pretix will generate one itself and save it to the filesystem.
| `pretix_django_debug` | Whether Django should run in debug mode  | `false` | **Never enable in production!**
| `pretix_django_profile` | Enable code profiling for a random subset of requests. | | [Documentation](https://docs.pretix.eu/en/latest/admin/maintainance.html#perf-monitoring)

#### Block: Metrics
Redis server is required for metrics-collection.

| Variable Name | Function | Default value | Comment |
| ------------- | -------- | ------------- | ------- |
| `pretix_metrics_enabled` | Whether metrics endpoint should be activated | false | `true`/`false`
| `pretix_metrics_user` | User for metrics endpoint _(required when metrics endpoint is enabled)_ | | 
| `pretix_metrics_passphrase` | Passphrase for user for metrics endpoint _(required when metrics endpoint is enabled)_ | | 

#### Block: Memcached
| Variable Name | Function | Default value | Comment |
| ------------- | -------- | ------------- | ------- |
| `pretix_memcached_location` | Location of memcached | | Either a host:port combination or a socket file  

#### Block: Redis
| Variable Name | Function | Default value | Comment |
| ------------- | -------- | ------------- | ------- |
| `pretix_redis_location` | The location of redis | | in URL form (either `redis://[:password]@localhost:6379/0` or `unix://[:password]@/path/to/socket.sock?db=0`) 
| `pretix_redis_session` | Whether redis should be used as session storage  | `true` |

#### Block: Celery
| Variable Name | Function | Default value | Comment |
| ------------- | -------- | ------------- | ------- |
| `pretix_celery_broker` | URL for celery broker _(required)_ | |
| `pretix_celery_backend` | URL for celery backend _(required)_  | |

#### Block: Sentry
| Variable Name | Function | Default value | Comment |
| ------------- | -------- | ------------- | ------- |
| `pretix_sentry_dsn` | Sentry DSN (created by sentry installation) | |

#### Other Blocks
You can put any blocks not mentioned above into the `pretix_additional_config` variable. The value of this variable is put at the end of pretix.cfg

## Dependencies
This role does not have any dependencies.

## Example Playbook

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:
```yaml
- hosts: servers
  roles:
     - {
        role: em0lar.pretix,
        pretix_version: "v2.2.0",
        pretix_bind_address: "127.0.0.1:8000",
        pretix_pretix_instance_name: "Example Pretix",
        pretix_pretix_url: "https://pretix.example.org",
        pretix_database_backend: "postgresql",
        pretix_database_name: "pretix",
        pretix_database_user: "pretix",
        pretix_database_password: "supersecurepassword",
        pretix_database_host: "localhost",
        pretix_celery_broker: "redis://localhost:6379/0",
        pretix_celery_backend: "redis://localhost:6379/0"
      }
```
## License

GPL-3.0