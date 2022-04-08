
# Ansible Role:  `logrotate`

Installs logrotate and provides an easy way to setup additional logrotate scripts by
specifying a list of directives.


[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/bodsch/ansible-logrotate/CI/master)][ci]
[![GitHub issues](https://img.shields.io/github/issues/bodsch/ansible-logrotate)][issues]
[![GitHub release (latest by date)](https://img.shields.io/github/v/release/bodsch/ansible-logrotate)][releases]

[ci]: https://github.com/bodsch/ansible-logrotate/actions
[issues]: https://github.com/bodsch/ansible-logrotate/issues?q=is%3Aopen+is%3Aissue
[releases]: https://github.com/bodsch/ansible-logrotate/releases


## Requirements & Dependencies

None

### Operating systems

Tested on

* ArchLinux
* Debian based
    - Debian 10 / 11
    - Ubuntu 20.04
* RedHat based
    - Alma Linux 8
    - Rocky Linux 8
    - OracleLinux 8

## usage

```yaml
logrotate_global:
  rotate_log: weekly
  rotate_size: ''
  su_user: ''
  su_group: ''
  rotate: 2
  create: true
  dateext: true
  compress: true
  tabooext: []
  archive_directory: ''

logrotate_conf_dir: "/etc/logrotate.d"

logrotate_scripts: {}

logroate_disable_systemd: true
```

###  **logrotate_scripts**: A dictionary of logrotate scripts and the directives to use for the rotation.

* `state` - create (`present`) or remove (`absent`) configuration. default: `present`
* `path` - Path to point logrotate to for the log rotation
* `paths` - A list of paths to point logrotate to for the log rotation.
* `options` - List of directives for logrotate, view the logrotate man page for specifics
* `scripts` - Dict of scripts for logrotate (see Example below)

```yaml
logrotate_scripts:
  audit:
    path: /var/log/audit/audit.log
    description: |
      rotate all audit logs
    options:
      - weekly
      - rotate 4
      - missingok
      - notifempty
      - delaycompress
    scripts:
      prerotate: systemctl stop auditd.service > /dev/null
      postrotate: systemctl start auditd.service > /dev/null
      foo: failed
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

## Example Playbook

see into [molecule test](molecule/default/converge.yml) and [configuration](molecule/default/group_vars/all/vars.yml)


## Contribution

Please read [Contribution](CONTRIBUTING.md)

## Development,  Branches (Git Tags)

The `master` Branch is my *Working Horse* includes the "latest, hot shit" and can be complete broken!

If you want to use something stable, please use a [Tagged Version](https://gitlab.com/bodsch/ansible-logrotate/-/tags)!



## Tests


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


## Author

- Bodo Schulz

## License

[Apache](LICENSE)

`FREE SOFTWARE, HELL YEAH!`
