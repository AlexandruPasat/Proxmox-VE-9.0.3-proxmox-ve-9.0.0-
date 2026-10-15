# Proxmox VE Production Environment

Production Proxmox VE environment with ZFS storage, Windows/Linux virtual machines and Zabbix monitoring.

## Platform

| Component | Version / value |
|---|---|
| Server | Dell PowerEdge R740 |
| Proxmox VE manager | `9.0.3` |
| Proxmox VE meta package | `9.0.0` |
| Debian | `13 (trixie)` |
| Kernel | `6.14.8-2-pve` |
| QEMU Server | `9.0.16` |
| ZFS | `2.3.3-pve1` |
| Proxmox Backup Client | `4.0.9-1` |
| Zabbix Agent2 | `7.0.31` |
| CPU | Intel Xeon Silver 4208, 8 cores / 16 threads |
| Memory | ~187 GiB usable |
| Node | `proxmox` |
| Architecture | `x86_64` |

## Environment overview

The production host uses two ZFS pools:

- `rpool` — mirror across two ~480 GB SSDs; Proxmox system and local ZFS storage
- `vm-storage` — RAIDZ2 across six ~600 GB disks; dedicated VM storage

Both pools were `ONLINE` in the captured production state and their latest scrub completed with zero errors.

The host currently runs six QEMU VMs and no LXC containers.

## Current workload placement

The live environment is in a mixed storage state:

- VM 100 is located on `vm-storage`
- multiple VM disks for VMIDs 101-105 are still present on `rpool/data`

The target design is to keep the Proxmox system on the SSD mirror and move VM workloads to dedicated VM storage where appropriate.

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

1. **Proxmox VE API** using the `Proxmox VE by HTTP` template.
2. **Zabbix Agent2** for host-level metrics and custom ZFS checks.

Monitored areas include:

- node CPU and memory
- network interfaces
- storage capacity
- VM discovery and status
- ZFS pool health
- ZFS capacity
- ZFS error state
- scrub status

## Backup state

The captured `/etc/pve/jobs.cfg` contained no configured jobs. No scheduled PVE backup job was therefore visible in this export. External backup mechanisms, if any, are outside the scope of the captured data.

## Security

This repository intentionally excludes production secrets and unique infrastructure identifiers.

Do not commit:

- API token secrets
- passwords
- private SSH keys
- private certificates
- `/etc/pve/priv/`
- cluster authentication keys
- backup encryption keys
- hardware service tags / disk serial numbers
- live management IP addresses

Use placeholders such as `<PROXMOX_MGMT_IP>`, `<API_TOKEN_ID>` and `<API_TOKEN_SECRET>` in public documentation.

## Documentation source

The repository was validated against a sanitized live export of the production host. Raw exports are reviewed before any information is committed to GitHub.
