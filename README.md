# [users](#users)

The purpose of this role is to add users and groups on your system.

|GitHub|GitLab|Quality|Downloads|Version|
|------|------|-------|---------|-------|
|[![github](https://github.com/robertdebock/ansible-role-users/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-users/actions)|[![gitlab](https://gitlab.com/robertdebock/ansible-role-users/badges/master/pipeline.svg)](https://gitlab.com/robertdebock/ansible-role-users)|[![quality](https://img.shields.io/ansible/quality/29201)](https://galaxy.ansible.com/robertdebock/users)|[![downloads](https://img.shields.io/ansible/role/d/29201)](https://galaxy.ansible.com/robertdebock/users)|[![Version](https://img.shields.io/github/release/robertdebock/ansible-role-users.svg)](https://github.com/robertdebock/ansible-role-users/releases/)|

## [Example Playbook](#example-playbook)

This example is taken from `molecule/resources/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: robertdebock.users
      users_group_list:
        - name: robertdb
          gid: 1024
        - name: users
        - name: notgroup
          state: absent
        - name: systemgroup
          system: yes

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
          authorized_keys:
            - "ssh-rsa ABC123"
          expires: -1
          password_validity_days: 9
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
        - name: passuser
          password: "$6$mysecretsalt$qJbapG68nyRab3gxvKWPUcs2g3t0oMHSHMnSKecYNpSi3CuZm.GbBqXO8BE6EI6P1JUefhA0qvD7b5LSh./PU1"
          update_password: on_create
        - name: remotekey
          authorized_keys:
            - "https://raw.githubusercontent.com/shaanr/smdb/master/file.pub"
        - name: systemuser
          system: yes
```

The machine needs to be prepared in CI this is done using `molecule/resources/prepare.yml`:
```yaml
---
- name: Prepare
  hosts: all
  gather_facts: no
  become: yes

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.core_dependencies
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for users

# The location to store ssh keys for user
users_ssh_key_directory: ssh_keys

# The default shell if not overwritten.
users_shell: /bin/bash

# manage cron permissions via /etc/cron.allow
users_cron_allow: yes

# should homedirectories be created?
users_create_home: yes
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/robertdebock/ansible-role-users/blob/master/requirements.txt).

## [Status of requirements](#status-of-requirements)

The following roles are used to prepare a system. You may choose to prepare your system in another way, I have tested these roles as well.

| Requirement | GitHub | GitLab |
|-------------|--------|--------|
| [robertdebock.bootstrap](https://galaxy.ansible.com/robertdebock/bootstrap) | [![Build Status GitHub](https://github.com/robertdebock/ansible-role-bootstrap/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-bootstrap/actions) | [![Build Status GitLab ](https://gitlab.com/robertdebock/ansible-role-ansible-role-bootstrap/badges/master/pipeline.svg)](https://gitlab.com/robertdebock/ansible-role-bootstrap)
| [robertdebock.core_dependencies](https://galaxy.ansible.com/robertdebock/core_dependencies) | [![Build Status GitHub](https://github.com/robertdebock/ansible-role-core_dependencies/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-core_dependencies/actions) | [![Build Status GitLab ](https://gitlab.com/robertdebock/ansible-role-ansible-role-core_dependencies/badges/master/pipeline.svg)](https://gitlab.com/robertdebock/ansible-role-core_dependencies)

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/ansible-role-users/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/robertdebock):

|container|tags|
|---------|----|
|alpine|all|
|amazon|Candidate|
|el|7, 8|
|debian|buster, bullseye|
|fedora|all|
|opensuse|all|
|ubuntu|focal, bionic|

The minimum version of Ansible required is 2.10, tests have been done to:

- The previous version.
- The current version.
- The development version.



If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-users/issues)

## [License](#license)

Apache-2.0

## [Contributors](#contributors)

I'd like to thank everybody that made contributions to this repository. It motivates me, improves the code and is just fun to collaborate.

- [aindenko](https://github.com/aindenko)
- [jkirk](https://github.com/jkirk)
- [icklers](https://github.com/icklers)

## [Author Information](#author-information)

[Robert de Bock](https://robertdebock.nl/)

Please consider [sponsoring me](https://github.com/sponsors/robertdebock).
