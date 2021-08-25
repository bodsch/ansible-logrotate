
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

* `name` - The name of the script that goes into /etc/logrotate.d/
* `path` - Path to point logrotate to for the log rotation
* `paths` - A list of paths to point logrotate to for the log rotation.
* `options` - List of directives for logrotate, view the logrotate man page for specifics
* `scripts` - Dict of scripts for logrotate (see Example below)

```yaml
logrotate_scripts:
  - name: rails
    path: "/srv/current/log/*.log"
    options:
      - weekly
      - size 25M
      - missingok
      - compress
      - delaycompress
      - copytruncate
```

```yaml
logrotate_scripts:
  - name: rails
    paths:
        - "/srv/current/scare.log"
        - "/srv/current/hide.log"
    options:
      - weekly
      - size 25M
      - missingok
      - compress
      - delaycompress
      - copytruncate
```

## Dependencies

None

## Example Playbook

Setting up logrotate for additional Nginx logs, with postrotate script.

```yaml
- hosts: all
  vars:
    logrotate_scripts:
      - name: nginx-options
        path: /var/log/nginx/options.log
        options:
          - daily
          - weekly
          - size 25M
          - rotate 7
          - missingok
          - compress
          - delaycompress
          - copytruncate

      - name: nginx-scripts
        path: /var/log/nginx/scripts.log
        options:
          - daily
          - weekly
          - size 25M
        scripts:
          postrotate: "echo test"

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
