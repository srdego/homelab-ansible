# Homelab Ansible Playbooks - Proxmox Role

## Purpose

This role hardens a single-node Proxmox VE host that runs on non-enterprise SSDs.
It strips enterprise subscription repositories, applies a safe upgrade workflow,
and reduces unnecessary write amplification so consumer drives avoid premature wear.

## Tags

| Tag                | Scope                                           | When to use                                                           |
| ------------------ | ----------------------------------------------- | --------------------------------------------------------------------- |
| `non_subscription` | Repo removal/addition + cache refresh           | Run only the repo flip when converting a node off enterprise feeds.   |
| `fresh_install`    | Cache refresh, dist upgrade, package install    | Apply the post-install bootstrap without touching service/log tuning. |
| `upgrade`          | Cache refresh + dist upgrade                    | Perform routine patching runs on an already-configured node.          |
| `single_node`      | HA/cluster service disablement                  | Skip on multi-node clusters; run intentionally on lone hosts.         |
| `ssd_health`       | HA disablement, fstrim timer, journald settings | Target IO-wear mitigations independently of lifecycle tasks.          |

Examples:

- Full run: `ansible-playbook playbooks/proxmox.yaml`
- Only upgrade path: `ansible-playbook playbooks/proxmox.yaml --tags upgrade`
- Only SSD-focused ops: `ansible-playbook playbooks/proxmox.yaml --tags ssd_health`
