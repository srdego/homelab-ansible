# Homelab Ansible Playbooks - Proxmox Role

Hardens and maintains a single-node Proxmox VE homelab deployment running on
non-subscription hardware feeds. The role is idempotent and can be run as part
of fresh installs, recurring upgrades, or targeted maintenance chunks via tags.

## Requirements

- Proxmox VE `9.x` host reachable via SSH.
- Control node with Ansible `2.20+` and access to the SSH public key declared in
  `proxmox_ssh_pub_key_path`.
- `sudo` privileges on the target host.

## Variables

All defaults live in `vars/main.yaml`. Override them from inventory, group vars,
or host vars as needed.

| Variable                                     | Purpose                                                           |
| -------------------------------------------- | ----------------------------------------------------------------- |
| `proxmox_apt_repositories_to_remove`         | Removes subscription-only repos.                                  |
| `proxmox_apt_repositories_to_add`            | Adds non-subscription repository.                                 |
| `proxmox_apt_packages_to_install`            | Utility packages installed during `fresh_install`.                |
| `proxmox_ha_and_cluster_services_to_disable` | Services stopped/disabled for solo nodes.                         |
| `proxmox_trim_timer_unit`                    | Ensures SSD trimming keeps running.                               |
| `proxmox_journald_storage_value`             | Forces journald to log in RAM to limit SSD wear.                  |
| `proxmox_ssh_pub_key_path`                   | Public key file on the control node that gets pushed to the host. |
| `proxmox_ssh_authorized_accounts`            | List of user/key pairs managed via `authorized_key`.              |
| `proxmox_sshd_directives`                    | Managed `sshd_config` hardening directives.                       |

## Handlers

- `Restart systemd-journald` – triggered when journald storage is changed.
- `Restart sshd` – async restart to apply hardened settings without hanging the run.

## Tags

| Tag                | Scope                                          | Typical use                                |
| ------------------ | ---------------------------------------------- | ------------------------------------------ |
| `non_subscription` | Repo removal/addition + cache refresh          | Convert a host away from enterprise feeds. |
| `fresh_install`    | Cache refresh, dist-upgrade, package bootstrap | Day-0 post-install setup.                  |
| `upgrade`          | Cache refresh + dist-upgrade                   | Routine patch cycle.                       |
| `single_node`      | HA/cluster service disablement                 | Skip on multi-node clusters.               |
| `ssd_health`       | Journald tuning + HA disablement + trim timer  | Focus on SSD wear mitigation.              |
| `ssh_hardening`    | Authorized keys + `sshd_config` directives     | Enforce pub-key-only access.               |

## Example Usage

No need to provide inventory as it's picked up from `ansible.cfg` by default.

```bash
# Full configuration
ansible-playbook playbooks/proxmox.yaml

# Only apply upgrades
ansible-playbook playbooks/proxmox.yaml --tags upgrade

# Tweak SSD-related services without touching packages
ansible-playbook playbooks/proxmox.yaml --tags ssd_health

# Re-run SSH hardening when rotating keys
ansible-playbook playbooks/proxmox.yaml --tags ssh_hardening
```

## Inventory Snippet

```ini
[proxmox]
bm-01 ansible_host=10.0.0.10 ansible_user=root

[proxmox:vars]
proxmox_ssh_pub_key_path=~/.ssh/id_ed25519.pub
```

## License

Released under the MIT License. See `LICENSE` for the full text.

## Author

[Jaime de Diego](mailto:art-breach-capitol@duck.com)
