# Proxmox VE Production Environment

Production Proxmox VE environment with ZFS storage, virtual machines, backup strategy and Zabbix monitoring.

## Platform

| Component | Version / value |
|---|---|
| Proxmox VE manager | `9.0.3` |
| Proxmox VE meta package | `9.0.0` |
| Kernel | `6.14.8-2-pve` |
| QEMU Server | `9.0.16` |
| ZFS | `2.3.3-pve1` |
| Proxmox Backup Client | `4.0.9-1` |
| Node | `proxmox` |
| Architecture | `x86_64` |

## Environment overview

This project documents a production Proxmox deployment running on Dell EMC server hardware.

Main design:

- Proxmox VE hypervisor
- ZFS-based storage
- `rpool` for the Proxmox system
- `vm-storage` RAIDZ2 pool for VM storage
- Windows and Linux virtual machines
- Zabbix Agent2 and Proxmox API monitoring
- custom ZFS health monitoring
- backup and restore validation procedures

## Repository structure

```text
.
├── README.md
├── .gitignore
├── configs/
│   └── zfs-monitoring.conf
└── docs/
    ├── architecture.md
    ├── storage.md
    ├── networking.md
    ├── vm-setup.md
    ├── backup-recovery.md
    ├── zabbix-monitoring.md
    └── live-config-export.md
```

## Monitoring

The node is monitored by Zabbix using two methods:

1. **Proxmox VE API** using the official `Proxmox VE by HTTP` template.
2. **Zabbix Agent2** for host-level metrics and custom ZFS checks.

Monitored areas include:

- node CPU and memory
- network interfaces
- storage capacity
- VM discovery and VM status
- ZFS pool health
- ZFS capacity
- ZFS error state
- scrub status

## Security

This repository intentionally excludes production secrets.

Do not commit:

- API token secrets
- passwords
- private SSH keys
- private certificates
- `/etc/pve/priv/`
- cluster authentication keys
- backup encryption keys

Use placeholders such as `<PROXMOX_MGMT_IP>`, `<API_TOKEN_ID>` and `<API_TOKEN_SECRET>` in documentation.

## Notes

Live configuration files should be reviewed and sanitized before being added to this repository. See [`docs/live-config-export.md`](docs/live-config-export.md).
