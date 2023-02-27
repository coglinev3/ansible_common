# Ansible Role: ansible-common

[![Build Status](https://travis-ci.com/coglinev3/ansible-common.svg?branch=master)](https://travis-ci.com/coglinev3/ansible-common) ![GitHub tag (latest by date)](https://img.shields.io/github/v/tag/coglinev3/ansible-common) [![License](https://img.shields.io/badge/License-BSD%203--Clause-blue.svg)](https://raw.githubusercontent.com/coglinev3/ansible-common/master/LICENSE)

Setup defaults (Ansible module dependencies, software packages) for every supported Linux distribution:
* Alpine Linux 3.14,
* Alpine Linux 3.15,
* Alpine Linux 3.16,
* Alpine Linux 3.17,
* Debian 8 (Jessie),
* Debian 9 (Stretch),
* Debian 10 (Buster),
* Enterprise Linux 7, 
* Enterprise Linux 8, 
* Ubuntu 18.04 LTS (Bionic Beaver) and
* Ubuntu 20.04 LTS (Focal Fossa).

This role is designed to run on every system as a initial setup. On the one hand, essential packages for Ansible modules and, on the other hand, standard packages for each Linux system are installed. Since each system administrator uses other Ansible modules, they can be defined using the `essential_packages` variable itself. The same applies to the standard packages. Because each system administrator or company has its own preferences for the packages to install on each system, those packages can be specified with the variable `common_packages`.

The role was tested with Docker on Travis-CI and with this [Multi-VM Vagrant environment](https://ansible-development.readthedocs.io/ "Vagrant environment for Developing and Testing Ansible Roles").

## Requirements

* On Ubuntu servers, the python-minimal or python3-minimal package must be installed before you can
execute this role.
* On Alpine Linux servers, the Python package must be installed before you can
execute this role.

## Role Variables

Available variables are listed below, along with default values (see defaults/main.yml):

```yml
# Essential packages for some ansible modules. The defaults provided by this
# role are specific to each distribution.

essential_packages:
  - bzip2
  - git
  - python3-apt
  - python3-httplib2
  - python3-jinja2
  - python3-lxml
  - python3-selinux
  - python3-semanage
  - python3-yaml
  - rsync
  - unzip
  - zip

# Standard packages for every system. The defaults provided by this role are
# specific to each distribution. You can define your own packages for example
# over a group_var or host_var definition.

common_packages:
  - curl
  - htop
  - net-tools
  - psmisc
  - vim
  - vim-common

# default package states: present | latest | absent
essential_packages_state: present
common_packages_state: present

# upgrade all packages to the latest version: true | false
common_packages_update_all: false

# update package cache: yes | no
common_packages_update_cache: "no"
```

## Dependencies

None.

## Example Playbook

```yml
---
# file: roles/ansible-common/tests/test.yml

- hosts: all
  become: true
  roles:
    - { role: coglinev3.ansible-common }
```

Using a pre_tasks statement on Ubuntu systems can ensure that the python-minimal or python3-minimal package is installed. Thanks for discussion on [gist.github.com](https://gist.github.com/gwillem/4ba393dceb55e5ae276a87300f6b8e6f "gwillem/ansible-bootstrap-ubuntu-16.04.yml")

```yml
---
- hosts: ubuntu-nodes
  gather_facts: False
  become: true
  # This will install python3 if missing on ubuntu (but checks first so no
  # expensive repeated apt updates)
  pre_tasks:
    - name: Install python for Ansible
      raw: test -e /usr/bin/python3 || (apt -y update && apt install -y python3-minimal)
      register: output
      changed_when: output.stdout != ""
    - setup: # aka gather_facts

- hosts: all
  become: true
  roles:
    - { role: coglinev3.ansible-common }
```

## Version

Release: 1.8.0

## License

BSD

## Author Information

This Ansible Role was created in 2018, by Cogline.v3.
