
# Ansible Role:  `logrotate`

Installs logrotate and provides an easy way to setup additional logrotate scripts by
specifying a list of directives.


[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/bodsch/ansible-logrotate/CI/master)][ci]
[![GitHub issues](https://img.shields.io/github/issues/bodsch/ansible-logrotate)][issues]
[![GitHub release (latest by date)](https://img.shields.io/github/v/release/bodsch/ansible-logrotate)][releases]

[ci]: https://github.com/bodsch/ansible-logrotate/actions
[issues]: https://github.com/bodsch/ansible-logrotate/issues?q=is%3Aopen+is%3Aissue
[releases]: https://github.com/bodsch/ansible-logrotate/releases

## tested operating systems

* Debian 9 / 10
* Ubuntu 18.04 / 20.04
* CentOS 8
* Oracle Linux 8

## Requirements

None

## Role Variables

**logrotate_scripts**: A list of logrotate scripts and the directives to use for the rotation.

* `state` - create (`present`) or remove (`absent`) configuration. default: `present`
* `path` - Path to point logrotate to for the log rotation
* `paths` - A list of paths to point logrotate to for the log rotation.
* `options` - List of directives for logrotate, view the logrotate man page for specifics
* `scripts` - Dict of scripts for logrotate (see Example below)

```yaml
logrotate_scripts:
  audit:
    path: /var/log/audit/audit.log
    options:
      - weekly
      - rotate 4
      - missingok
      - notifempty
      - delaycompress
    scripts:
      postrotate: /etc/init.d/auditd restart2> /dev/null
```

```yaml
logrotate_scripts:
  nginx:
    paths:
      - /var/log/nginx/*/*.log
      - /var/log/nginx/*.log
    options:
      - weekly
      - rotate 2
      - missingok
      - notifempty
      - compress
      - sharedscripts
      - create 0644 http log
      - su root http
    scripts:
      postrotate: test ! -r /run/nginx.pid || kill -USR1 $(cat /run/nginx.pid)
```

## Dependencies

None

## Example Playbook

Setting up logrotate for additional Nginx logs, with postrotate script.

```yaml
- hosts: all
  roles:
    - ansible-logrotate
```

## Tests

### Testmatrix

|                                | Python Version | Ansible Version |
| :----------------------        | :------        | :------         |
| `py37-ansible28`               | *python 3.7*   | *ansible 2.8*   |
| `py38-ansible28`               | *python 3.8*   | *ansible 2.8*   |
| `py38-ansible29`               | *python 3.8*   | *ansible 2.9*   |

```bash
tox -e py38-ansible29 -- molecule test --all
```


### Syntactic tests
A syntactical test checks the correctness of the created YAML files.

```bash
tox -e py38-ansible29 -- molecule lint --all
```

### Test without deleting the test instance

```bash
tox -e py38-ansible29 -- molecule converge --all
```

### Validierung

```bash
tox -e py38-ansible29 -- molecule verify --all
```

## Delete the Docker Container

```bash
tox -e py38-ansible29 -- molecule destroy --all
```



## License

[Apache](https://raw.githubusercontent.com/bodsch/ansible-logrotate/master/LICENSE)
