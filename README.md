# Ansible Role: proxmox_ha

[![Lint](https://github.com/dcjulian29/ansible-role-proxmox_ha/actions/workflows/lint.yml/badge.svg)](https://github.com/dcjulian29/ansible-role-proxmox_ha/actions/workflows/lint.yml) [![GitHub Issues](https://img.shields.io/github/issues-raw/dcjulian29/ansible-role-proxmox_ha.svg)](https://github.com/dcjulian29/ansible-role-proxmox_ha/issues)

Ansible role to configure high-availability for Proxmox guests. It puts a guest
under HA management and adds it to the HA rules it names, and takes it back out
before the guest is destroyed. It is called by the guest roles
(`dcjulian29.proxmox_kvm_create`, `dcjulian29.proxmox_kvm_clone`,
`dcjulian29.proxmox_lxc_create`) rather than applied on its own.

## Requirements

- Proxmox VE 9 or later (HA rules replaced HA groups in 9.0).
- `community.proxmox` 2.0.0 or later, and `proxmoxer` on the Proxmox node.
- Guests on shared storage: a guest on node-local storage has nowhere to
  fail over to.

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

## Usage

Define the rules once, keyed by name (resources are never listed; each guest
adds itself):

```yaml
proxmox_ha_rules:
  prefer-host1:
    type: node-affinity
    nodes: [host1:2, host2:1]
  split-media:
    type: resource-affinity
    affinity: negative
```

A guest role calls the two entry points around its destroy and create:

```yaml
- name: Take the guest out of HA before it is stopped and destroyed
  ansible.builtin.include_role:
    name: dcjulian29.proxmox_ha
    tasks_from: leave
  vars:
    proxmox_ha_resource: "vm:{{ vmid }}"

- name: Put the guest under HA and into its rules once it is running
  ansible.builtin.include_role:
    name: dcjulian29.proxmox_ha
    tasks_from: join
  vars:
    proxmox_ha_resource: "vm:{{ vmid }}"
    proxmox_ha_rule_names: [prefer-odin, split-media]
```

`join` creates a rule that does not exist yet with the guest as its only
member, and otherwise adds the guest to the rule's members. `leave` removes the
HA resource, which on PVE 9 also takes the guest out of every rule and deletes
any rule it was the last member of.
