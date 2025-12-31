# Homelab Ansible Playbooks - Proxmox Role

## Purpose

This role targets single-node homelab Proxmox VE deployments that rely on
non-enterprise harware. It uses non-subscription repos, applies lifecycle tasks,
tunes services, and enforces SSH pub-key-only authentication.

## Tags

| Tag                | Scope                                           | When to use                                                           |
| ------------------ | ----------------------------------------------- | --------------------------------------------------------------------- |
| `non_subscription` | Repo removal/addition + cache refresh           | Run only the repo flip when converting a node off enterprise feeds.   |
| `fresh_install`    | Cache refresh, dist upgrade, package install    | Apply the post-install bootstrap without touching service/log tuning. |
| `upgrade`          | Cache refresh + dist upgrade                    | Perform routine patching runs on an already-configured node.          |
| `single_node`      | HA/cluster service disablement                  | Skip on multi-node clusters; run intentionally on lone hosts.         |
| `ssd_health`       | HA disablement, fstrim timer, journald settings | Target IO-wear mitigations independently of lifecycle tasks.          |
| `ssh_hardening`    | SSH config updates                              | Apply pub-key-only authentication.                                    |

Examples:

- Full run: `ansible-playbook playbooks/proxmox.yaml`
- Only upgrade path: `ansible-playbook playbooks/proxmox.yaml --tags upgrade`
- Only SSD-focused ops: `ansible-playbook playbooks/proxmox.yaml --tags ssd_health`
- Only SSH hardening: `ansible-playbook playbooks/proxmox.yaml --tags ssh_hardening`
