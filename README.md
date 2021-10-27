Role Name
=========

An ansible role to easily deploy new systemd services. Used for Shellphish's [iCTF](https://github.com/shellphish/ictf-framework) competition, but can be as generic as wanted, any PRs are welcome.

Requirements
------------

This role registers systemd services, so systemd must be installed and running.

Role Variables
--------------

The variable structure closely mimics systemd's Unit files to be self explanatory. Some reasonable defaults are set to reduce the amount of boilerplate code.

An up-to-date example using all currently supported variables should always be found in `tests/full.yml`, but it looks like this:
```
    - name: install a random file dumper as a systemd service
      include_role:
        name: register_systemd_service
      vars:

        Name: r4nD0m
        Enabled: true

        Unit:
          Description: Crazy amounts of randomness
          After:
            - network.target
            - multi-user.target
          Requires:
            - network.target
            - multi-user.target
          AssertPathExists:
            - # to get rid of all other asserts
            - /
            - /tmp
            - /dev/urandom
            - /bin/bash

        Service:
          WorkingDirectory: /tmp
          Environment:
            ASDF: "abc"
            DEFG: "def"
          Restart: always
          User: root
          ExecStart: "/bin/bash -c 'while true; do head -n 3 /dev/urandom | tee /tmp/r4Nd0m; echo $ASDF >> /tmp/r4Nd0m; sleep 10; done'"

        Install:
          WantedBy: multi-user.target
```
As you can see most variables directly match the corresponding structure in the Unit files.


Dependencies
------------

None

Example Playbook
----------------

A minimal example of a service can be created with the following playbook. A service requires at least a name, a working directory and a command to

```yaml
  tasks:
    - name: install a random file dumper as a systemd service
      include_role:
        name: register_systemd_service
      vars:
        Name: r4nD0m
        Service:
          WorkingDirectory: /tmp
          ExecStart: "/bin/bash -c 'while true; do head -n 3 /dev/urandom | tee /tmp/r4Nd0m; sleep 10; done'"
```

This creates the following service unit file in `/etc/systemd/system/r4nD0m.service`:
```
# Service file created by register_systemd_service for Service r4nD0m

[Unit]
Description = r4nD0m
After = network.target

AssertPathExists = /tmp

[Service]
WorkingDirectory= /tmp

ExecStart=/bin/bash -c 'while true; do head -n 3 /dev/urandom | tee /tmp/r4Nd0m; sleep 10; done'
Restart= always

[Install]
```

License
-------

MIT