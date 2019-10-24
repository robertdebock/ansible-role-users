users
=========

<img src="https://docs.ansible.com/ansible-tower/3.2.4/html_ja/installandreference/_static/images/logo_invert.png" width="10%" height="10%" alt="Ansible logo" align="right"/>
<a href="https://travis-ci.org/robertdebock/ansible-role-users"><img src="https://travis-ci.org/robertdebock/ansible-role-users.svg?branch=master" alt="Build status" align="left"/></a>

The purpose of this role is to add users and groups on your system.

<img src="https://img.shields.io/ansible/role/d/29201"/>
<img src="https://img.shields.io/ansible/quality/29201"/>

Example Playbook
----------------

This example is taken from `molecule/resources/playbook.yml`:
```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: yes

  vars:
    users_group_list:
      - name: robertdb
        gid: 1024
      - name: users
      - name: notgroup
        state: absent

    users_user_list:
      - name: root
        cron_allow: yes
      - name: robertdb
        comment: Robert de Bock
        uid: 1024
        group: robertdb
        groups: users
        cron_allow: yes
        sudo_options: "ALL=(ALL) NOPASSWD: ALL"
        authorized_key: "ssh-rsa ABC123"
      - name: notuser
        state: absent
      - name: keyuser
        manage_ssh_key: yes
      - name: privkeyuser
        manage_ssh_key: yes
        copy_private_key: yes
      - name: multiplekeys
        authorized_keys:
          - "ssh-rsa ABC1234"
          - "ssh-rsa ABC12345"
      - name: mixedkeys
        authorized_key: "ssh-rsa ABC123456"
        authorized_keys:
          - "ssh-rsa ABC1234567"
          - "ssh-rsa ABC12345678"

  roles:
    - robertdebock.users
```

The machine you are running this on, may need to be prepared.
```yaml
---
- name: Prepare
  hosts: all
  gather_facts: no
  become: yes

  roles:
    - robertdebock.bootstrap
    - robertdebock.core_dependencies
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

Role Variables
--------------

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for users

# The location to store ssh keys for user
users_ssh_key_directory: ssh_keys

# The default shell if not overwritten.
users_shell: /bin/bash

# manage cron permissions via /etc/cron.allow
users_cron_allow: true

# A list of groups and properties.
# users_group_list:
#   - name: robertdb
#     gid: 1024
#   - name: notgroup
#     state: absent

# A list of users and properties.
# users_user_list:
#   - name: root
#     cron_allow: yes
#   - name: robertdb
#     comment: Robert de Bock
#     uid: 1024
#     group: robertdb
#     groups: users,wheel
#     cron_allow: yes
#     sudo_options: "ALL=(ALL) NOPASSWD: ALL"
#     authorized_key: "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCWswOogkZz/ihQA0lENCwDwSzmtmBWtFwzIzDlfa+eb4rBt6rZBg7enKeMqYtStI/NDneBwZUFBDIMu5zJTbvg7A60/WDhWXZmU21tZnm8K7KREFYOUndc6h//QHig6IIaIwwBZHF1NgXLtZ0qrUUlNU5JSEhDJsObMlPHtE4vFP8twPnfc7hxAnYma5+knU6qTMCDvhBE5tGJdor4UGeAhu+SwSVDloYtt1vGTmnFn8M/OD/fRMksusPefxyshJ37jpB4jY/Z9vzaNHwcj33prwl1b/xRfxr/+KRJsyq+ZKs9u2TVw9g4p+XLdfDtzZ8thR2P3x3MFrZOdFmCbo/5"
#      authorized_keys:
#        - "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCWswOogkZz/ihQA0lENCwDwSzmtmBWtFwzIzDlfa+eb4rBt6rZBg7enKeMqYtStI/NDneBwZUFBDIMu5zJTbvg7A60/WDhWXZmU21tZnm8K7KREFYOUndc6h//QHig6IIaIwwBZHF1NgXLtZ0qrUUlNU5JSEhDJsObMlPHtE4vFP8twPnfc7hxAnYma5+knU6qTMCDvhBE5tGJdor4UGeAhu+SwSVDloYtt1vGTmnFn8M/OD/fRMksusPefxyshJ37jpB4jY/Z9vzaNHwcj33prwl1b/xRfxr/+KRJsyq+ZKs9u2TVw9g4p+XLdfDtzZ8thR2P3x3MFrZOdFmCbo/5"
#     copy_private_key: yes
#   - name: notuser
#     state: absent
#   - name: keyuser
#     manage_ssh_key: yes
#   - name: specificshell
#     shell: "/bin/ksh"
```

Requirements
------------

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

The following roles can be installed to ensure all requirements are met, using `ansible-galaxy install -r requirements.yml`:

```yaml
---
- robertdebock.bootstrap
- robertdebock.core_dependencies

```

This role uses the following modules:
```yaml
---
- authorized_key
- command
- copy
- file
- group
- include
- shell
- template
- user
```

Context
-------

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/users.png "Dependency")


Compatibility
-------------

This role has been tested on these [container images](https://hub.docker.com/):

|container|tag|allow_failures|
|---------|---|--------------|
|docker-alpine-openrc|latest|no|
|docker-alpine-openrc|edge|yes|
|docker-debian-systemd|stable|yes|
|docker-debian-systemd|unstable|yes|
|docker-debian-systemd|latest|no|
|docker-centos-systemd|7|no|
|docker-redhat-systemd|7|no|
|docker-centos-systemd|latest|no|
|docker-redhat-systemd|latest|no|
|docker-fedora-systemd|latest|no|
|docker-fedora-systemd|rawhide|yes|
|docker-opensuse-systemd|latest|no|
|docker-ubuntu-systemd|rolling|yes|
|docker-ubuntu-systemd|devel|yes|
|docker-ubuntu-systemd|latest|no|

This role has been tested on these Ansible versions:

- ansible~=2.7
- ansible~=2.8
- git+https://github.com/ansible/ansible.git@devel

The indicator '\~=' means [compatible with](https://www.python.org/dev/peps/pep-0440/#compatible-release). For example 'ansible\~=2.8' would pick the latest ansible-2.8, for example ansible-2.8.6.




Testing
-------

[Unit tests](https://travis-ci.org/robertdebock/ansible-role-users) are done on every commit and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-users/issues)

To test this role locally please use [Molecule](https://github.com/ansible/molecule):
```
pip install molecule
molecule test
```

To test on Amazon EC2, configure [~/.aws/credentials](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/credentials.html) and set a region using `export AWS_REGION=eu-central-1` before running `molecule test --scenario-name ec2`.

There are many specific scenarios available, please have a look in the `molecule/` directory.

License
-------

Apache-2.0


Author Information
------------------

[Robert de Bock](https://robertdebock.nl/)
