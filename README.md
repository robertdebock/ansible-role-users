# [users](#users)

The purpose of this role is to add users and groups on your system.

|Travis|GitHub|Quality|Downloads|Version|
|------|------|-------|---------|-------|
|[![travis](https://travis-ci.com/robertdebock/ansible-role-users.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-users)|[![github](https://github.com/robertdebock/ansible-role-users/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-users/actions)|[![quality](https://img.shields.io/ansible/quality/29201)](https://galaxy.ansible.com/robertdebock/users)|[![downloads](https://img.shields.io/ansible/role/d/29201)](https://galaxy.ansible.com/robertdebock/users)|[![Version](https://img.shields.io/github/release/robertdebock/ansible-role-users.svg)](https://github.com/robertdebock/ansible-role-users/releases/)|

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
```

The machine may need to be prepared using `molecule/resources/prepare.yml`:
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

For verification `molecule/resources/verify.yml` runs after the role has been applied.
```yaml
---
- name: Verify
  hosts: all
  become: yes
  gather_facts: no

  tasks:
    - name: record status of .ssh
      stat:
        path: /home/keyuser/.ssh
      register: result
    - name: check status of .ssh
      assert:
        that:
          - result.stat.exists
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
```

## [Requirements](#requirements)

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

The following roles can be installed to ensure all requirements are met, using `ansible-galaxy install -r requirements.yml`:

```yaml
---
- robertdebock.bootstrap
- robertdebock.core_dependencies

```

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/users.png "Dependency")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/robertdebock):

|container|tags|
|---------|----|
|alpine|all|
|amazon|2018.03|
|el|7, 8|
|debian|buster, bullseye|
|fedora|31, 32|
|opensuse|all|
|ubuntu|focal, bionic, xenial|

The minimum version of Ansible required is 2.9, tests have been done to:

- The previous version.
- The current version.
- The development version.



## [Testing](#testing)

[Unit tests](https://travis-ci.com/robertdebock/ansible-role-users) are done on every commit, pull request, release and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-users/issues)

Testing is done using [Tox](https://tox.readthedocs.io/en/latest/) and [Molecule](https://github.com/ansible/molecule):

[Tox](https://tox.readthedocs.io/en/latest/) tests multiple ansible versions.
[Molecule](https://github.com/ansible/molecule) tests multiple distributions.

To test using the defaults (any installed ansible version, namespace: `robertdebock`, image: `fedora`, tag: `latest`):

```
molecule test

# Or select a specific image:
image=ubuntu molecule test
# Or select a specific image and a specific tag:
image="debian" tag="stable" tox
```

Or you can test multiple versions of Ansible, and select images:
Tox allows multiple versions of Ansible to be tested. To run the default (namespace: `robertdebock`, image: `fedora`, tag: `latest`) tests:

```
tox

# To run CentOS (namespace: `robertdebock`, tag: `latest`)
image="centos" tox
# Or customize more:
image="debian" tag="stable" tox
```

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
