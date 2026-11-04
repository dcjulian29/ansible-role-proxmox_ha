# Ansible Role: proxmox_ha

[![Lint](https://github.com/dcjulian29/ansible-role-proxmox_ha/actions/workflows/lint.yml/badge.svg)](https://github.com/dcjulian29/ansible-role-proxmox_ha/actions/workflows/lint.yml) [![GitHub Issues](https://img.shields.io/github/issues-raw/dcjulian29/ansible-role-proxmox_ha.svg)](https://github.com/dcjulian29/ansible-role-proxmox_ha/issues)

Ansible role to configure high-availabilty for guest VMs

## Requirements

- Active Internet Connection.

## Installation

To use, use `requirements.yml` with the following git source:

```yaml
---
roles:
- name: dcjulian29.proxmox_ha
  src: https://github.com/dcjulian29/ansible-role-proxmox_ha.git
  version: main
  ```

Then download it with `ansible-galaxy`:

```shell
ansible-galaxy install -r requirements.yml
```

## Dependencies

- None
