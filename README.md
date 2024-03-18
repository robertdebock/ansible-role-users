# [Ansible role users](#users)

The purpose of this role is to add users and groups on your system.

|GitHub|GitLab|Downloads|Version|
|------|------|---------|-------|
|[![github](https://github.com/robertdebock/ansible-role-users/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-users/actions)|[![gitlab](https://gitlab.com/robertdebock-iac/ansible-role-users/badges/master/pipeline.svg)](https://gitlab.com/robertdebock-iac/ansible-role-users)|[![downloads](https://img.shields.io/ansible/role/d/robertdebock/users)](https://galaxy.ansible.com/robertdebock/users)|[![Version](https://img.shields.io/github/release/robertdebock/ansible-role-users.svg)](https://github.com/robertdebock/ansible-role-users/releases/)|

## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/robertdebock/ansible-role-users/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: Converge
  hosts: all
  become: true
  gather_facts: true

  roles:
    - role: robertdebock.users
      # You can create groups:
      users_groups:
        - name: robertdb
          gid: 1024
        - name: users
        # You can also remove groups.
        - name: notgroup
          state: absent
          # A system group is also possible.
        - name: systemgroup
          system: true

      # You can create users.
      users:
        - name: root
          cron_allow: true
          # You can remove authorized keys.
          unauthorized_keys:
            - "ssh-rsa XYZYX54321"
        - name: robertdb
          comment: Robert de Bock
          uid: 1024
          # The `group` and `groups` listed here should exist.
          group: robertdb
          # groups: A list of groups
          # groups:
          #   - users
          #   - wheel
          groups:
            - users
          cron_allow: true
          sudo_options: "ALL=(ALL) NOPASSWD: ALL"
          # Adding an authorized key.
          authorized_keys:
            - "ssh-rsa ABC123"
          # EPOCH timestamp when an account should expire.
          # Typically a positive value like: `1641971487`.
          # The value `-1` removes the expiry time.
          expires: -1
          password_validity_days: 9
          # Test username with dots
        - name: robert.d.b
          comment: Robert de Bock with dots in username
          uid: 1025
          # The `group` and `groups` listed here should exist.
          group: robertdb
          # groups: A list of groups
          # groups:
          #   - users
          #   - wheel
          groups:
            - users
          cron_allow: true
          # Allow sudo, but require a password.
          sudo_options: "ALL=(ALL) ALL"
          # Adding an authorized key.
          authorized_keys:
            - "ssh-rsa ABC123"
          # EPOCH timestamp when an account should expire.
          # Typically a positive value like: `1641971487`.
          # The value `-1` removes the expiry time.
          expires: -1
          password_validity_days: 9
        # Here a user is removed.
        - name: notuser
          state: absent
        - name: keyuser
          manage_ssh_key: true
        - name: privkeyuser
          # This user will have ssh-keys generated.
          manage_ssh_key: true
          copy_private_key: true
        - name: multiplekeys
          authorized_keys:
            - "ssh-rsa ABC1234"
            - "ssh-rsa ABC12345"
        - name: passuser
          # You can set a password. (Hashed and salted.)
          password: "$6$mysecretsalt$qJbapG68nyRab3gxvKWPUcs2g3t0oMHSHMnSKecYNpSi3CuZm.GbBqXO8BE6EI6P1JUefhA0qvD7b5LSh./PU1"
          update_password: on_create
        - name: remotekey
          authorized_keys:
            # You can also download a public key from a URL.
            - "https://raw.githubusercontent.com/shaanr/smdb/master/file.pub"
        - name: systemuser
          system: true
        - name: multisudo
          # An account that can run just a few commands without a password.
          sudo_options:
            - "ALL= NOPASSWD: /usr/bin/systemctl restart httpd"
            - "ALL= NOPASSWD: /usr/bin/systemctl start httpd"
            - "ALL= NOPASSWD: /usr/bin/systemctl stop httpd"
        - name: myprivkeyuser
          private_keys:
            - name: id_rsa
              content: |
                -----BEGIN OPENSSH PRIVATE KEY-----
                b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
                NhAAAAAwEAAQAAAYEA4CaHuEpYaSJOwVBU2vW+KVebLv33hGcRqGJrzHCcwlLAf4u+Eigm
                mtqD5qosHONAs07G3QyrViR1jTWrJh/1ShTyZ/Fv7AUM/4fFyWYEmt2Rd+ZprojzZ1HUe4
                Hlp4GV9cC28Ic7g1pglt4a4xHjhlt+J63xz+hOHbPWNfeB0Fl6+s7LB/JHbsti0qullhaf
                L2PYyH21FrCLVouOyQpHpAAjCGC264Dvke/LSReE7z7CrFc7pBZAd9McFIEVXNJPVRWOjZ
                IePXnDLclIYceqDJV1elP6bCb8l9m3K4k/pbHjVgjIRCLXsD+yCgPvnfUBRaK7IWZWqSc0
                EDJAt7+THeksVf4kamxzZlnWfmsJ323gvktlMjBhSfKdB6YXmD8MQGOPqATAbwed/1ZDAX
                L90GyzYNBwx+jUxCOOItGEMwXQEImaVO6nfiG5Zgn6MwjP2gQ3lDVZgjhaXJM6B3PZQU4t
                MLDl8jZeCmjbltppKQ6OJx8QwImrSWcXr+lFKh7FAAAFoP+Ytw7/mLcOAAAAB3NzaC1yc2
                EAAAGBAOAmh7hKWGkiTsFQVNr1vilXmy7994RnEahia8xwnMJSwH+LvhIoJprag+aqLBzj
                QLNOxt0Mq1YkdY01qyYf9UoU8mfxb+wFDP+HxclmBJrdkXfmaa6I82dR1HuB5aeBlfXAtv
                CHO4NaYJbeGuMR44Zbfiet8c/oTh2z1jX3gdBZevrOywfyR27LYtKrpZYWny9j2Mh9tRaw
                i1aLjskKR6QAIwhgtuuA75Hvy0kXhO8+wqxXO6QWQHfTHBSBFVzST1UVjo2SHj15wy3JSG
                HHqgyVdXpT+mwm/JfZtyuJP6Wx41YIyEQi17A/sgoD7531AUWiuyFmVqknNBAyQLe/kx3p
                LFX+JGpsc2ZZ1n5rCd9t4L5LZTIwYUnynQemF5g/DEBjj6gEwG8Hnf9WQwFy/dBss2DQcM
                fo1MQjjiLRhDMF0BCJmlTup34huWYJ+jMIz9oEN5Q1WYI4WlyTOgdz2UFOLTCw5fI2Xgpo
                25baaSkOjicfEMCJq0lnF6/pRSoexQAAAAMBAAEAAAGAVBC/R/PCErQGO2QoXvJZPmIX1l
                jzcpnBOpim+0ak1BB7F5rOTzr1t5AKdgEOiEsGofkChjbRL35/A9XD+Yl00jLm8urO4gLG
                7DL8j9sOERLWEtS8f/Pm7bH38dY2zkrAdimvsqfVPx8vKIXmKZE09tfriXAoR6RFX8hpp1
                XLPtvATr4t0S4hckEfMAmmEZMsYnadABx9RSQzLeMrSuTrF2vht6umDs8NinsXj7MdbSjp
                k/8nZBPgW9A2MM50t5o76LNGa2UwIlAtEQRTGcSyEqMTZ9TOndiNUT9vtx1/ddq6YwH8S3
                mF5CqLW92nOr8dE6lUwSmGgHquMKGNjjIWtJO9vdV6/m4SOo6Sxiq14w+NSlJpkGAssJlb
                lyxbRiKK3tEpLOLQMS4i0SK++MsDgyxfBgDKCpBlfQRs3/RJzWO82abTMuyJj3XkN3DkiN
                fppX4S7nVjtYV1TD4e5kMHgfKHHckvMgwzxrtyLB1r7D+qDcH3D7wUWJS4cs3ECuCFAAAA
                wC+Hw6tg38zIFBF4h2nqbYRy7ifGUT3Wo0I5V6rJtEwSMXx71ZocLH/synywdITu8koZD7
                JIYze4Zey9kdsgc8ZKTwSAsZoYqx8YKaICM61yQ9JaoyA9oFIFtaJ/+XLjAgoYr4VN3/T3
                Jv57DjEzl5FAAuWGkmSWDEEJiE1BcBH+QuodWIhdZrUdbbb26XjVBea3N2THckkavjj4eD
                m0tzzDO0VeTxM0bB+gG/bi07I8vs1EcDCAism29PdSEL6D8AAAAMEA+7N2Q9DK9MihH0+J
                TYcaucrPN6U09FKjo/2wbitO2/IbTsmHcilJUcCdXDMFPg6bSPl1MRzxBKnkfZO8xW9NRq
                lg4NL6SwaBj3stV/WqQpdCzrvu9mMPbu6dY6O8qvbv6GKknBlYiLjbeW+Ov43GYWKC3QNU
                w7d7Yx2wEfMmVACp4vsjhxgI6KEKAG6dlf25CbTvxRWYcaprrmMn0h9m8UOCRNPdPyOQ/t
                aIjGBEPHzOf/dxGCZXGQEKk9ABMsDXAAAAwQDj+psz4GORg10kHVxRwdkKRIUBTZSk8nzo
                ml45KYDkTnLtRxJvh8jy0/NLGU0xFEew3+MfZIMGOcU7cezXtLhMkEVtKCgCXq2kqMHzta
                rLGVWU0xcXVygKQmUva3kdscEHO1voRIzn1dZPOZQMIZoQysdOF0mUflbVVSE1why8lMkM
                wwWPYUZK7yTOYnZ08HusE9M0A77Th2SzTCDz2Vlw4YwJU0+4PD7i2wEGNgbzt/WnAj6kOG
                ql74i8K7EiPcMAAAAlcm9iZXJ0ZGJATWFjQm9vay1Qcm8tdmFuLVJvYmVydC5sb2NhbAEC
                AwQFBg==
                -----END OPENSSH PRIVATE KEY-----
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/robertdebock/ansible-role-users/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: Prepare
  hosts: all
  become: true
  gather_facts: false

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.core_dependencies

  tasks:
    - name: Set authorized keys for root
      ansible.posix.authorized_key:
        user: root
        state: present
        key: "ssh-rsa XYZYX54321"
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/robertdebock/ansible-role-users/blob/master/defaults/main.yml):

```yaml
---
# defaults file for users

# The location to store ssh keys for user
users_ssh_key_directory: ssh_keys

# The default shell if not overwritten.
users_shell: /bin/bash

# manage cron permissions via /etc/cron.allow
users_cron_allow: true

# should homedirectories be created?
users_create_home: true
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/robertdebock/ansible-role-users/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | GitLab |
|-------------|--------|--------|
|[robertdebock.bootstrap](https://galaxy.ansible.com/robertdebock/bootstrap)|[![Build Status GitHub](https://github.com/robertdebock/ansible-role-bootstrap/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-bootstrap/actions)|[![Build Status GitLab](https://gitlab.com/robertdebock-iac/ansible-role-bootstrap/badges/master/pipeline.svg)](https://gitlab.com/robertdebock-iac/ansible-role-bootstrap)|
|[robertdebock.core_dependencies](https://galaxy.ansible.com/robertdebock/core_dependencies)|[![Build Status GitHub](https://github.com/robertdebock/ansible-role-core_dependencies/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-core_dependencies/actions)|[![Build Status GitLab](https://gitlab.com/robertdebock-iac/ansible-role-core_dependencies/badges/master/pipeline.svg)](https://gitlab.com/robertdebock-iac/ansible-role-core_dependencies)|

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/ansible-role-users/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/robertdebock):

|container|tags|
|---------|----|
|[Alpine](https://hub.docker.com/r/robertdebock/alpine)|all|
|[Amazon](https://hub.docker.com/r/robertdebock/amazonlinux)|Candidate|
|[EL](https://hub.docker.com/r/robertdebock/enterpriselinux)|8, 9|
|[Debian](https://hub.docker.com/r/robertdebock/debian)|all|
|[Fedora](https://hub.docker.com/r/robertdebock/fedora)|all|
|[opensuse](https://hub.docker.com/r/robertdebock/opensuse)|all|
|[Ubuntu](https://hub.docker.com/r/robertdebock/ubuntu)|all|

The minimum version of Ansible required is 2.12, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-users/issues).

## [License](#license)

[Apache-2.0](https://github.com/robertdebock/ansible-role-users/blob/master/LICENSE).

## [Author Information](#author-information)

[robertdebock](https://robertdebock.nl/)

Please consider [sponsoring me](https://github.com/sponsors/robertdebock).
