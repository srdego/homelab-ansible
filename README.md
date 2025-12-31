# Homelab Ansible Playbooks

IaC for a personal homelab built around Proxmox VE, Talos OS cluster and other
services. Each playbook is intentionally small so that individual hosts can be
configured, patched, or hardened without dragging in unrelated changes.

## Requirements

- Ansible `2.20+` with Python `3.14` or newer on the control node.
- SSH access to each managed host with pub-key authentication.

## Available Playbooks

For detailed usage, see each playbook's README under `roles/`.

## Contributing

Issues and PRs are welcome. Keep roles idempotent, document new variables inside
each role README, and include tag coverage so targeted runs stay predictable.

## License

Released under the MIT License. See `LICENSE` for the full text.

## Author

[Jaime de Diego](mailto:art-breach-capitol@duck.com)
