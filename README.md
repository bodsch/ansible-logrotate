

Installs logrotate and provides an easy way to setup additional logrotate scripts by
specifying a list of directives.


[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/bodsch/ansible-logrotate/CI/master)][ci]
[![GitHub issues](https://img.shields.io/github/issues/bodsch/ansible-logrotate)][issues]
[![GitHub release (latest by date)](https://img.shields.io/github/v/release/bodsch/ansible-logrotate)][releases]

[ci]: https://github.com/bodsch/ansible-logrotate/actions
[issues]: https://github.com/bodsch/ansible-logrotate/issues?q=is%3Aopen+is%3Aissue
[releases]: https://github.com/bodsch/ansible-logrotate/releases


## Requirements

None

## Role Variables

**logrotate_scripts**: A list of logrotate scripts and the directives to use for the rotation.

* name - The name of the script that goes into /etc/logrotate.d/
* path - Path to point logrotate to for the log rotation
* paths - A list of paths to point logrotate to for the log rotation.
* options - List of directives for logrotate, view the logrotate man page for specifics
* scripts - Dict of scripts for logrotate (see Example below)

```
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

```
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

```
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

## Testing locally

This role is already configured to run on travis CI within a test playbook but it's useful to be able to run and debug a role locally which can be done via Vagrant and the `ansible_local` provisioner.

To run the test playbook locally within a Vagrant virtual machine:

```
cd tests
vagrant up --provision
```

## License

[Apache](https://raw.githubusercontent.com/bodsch/ansible-logrotate/master/LICENSE)
