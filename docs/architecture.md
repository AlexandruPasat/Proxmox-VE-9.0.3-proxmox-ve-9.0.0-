# Architecture

## Host

- Platform: Proxmox VE
- Node name: `proxmox`
- Management IP: `10.100.184.5`
- Hardware: Dell EMC rack server
- Architecture: `x86_64`

## Software baseline

- Proxmox VE manager: `9.0.3`
- Proxmox VE meta package: `9.0.0`
- Kernel: `6.14.8-2-pve`
- QEMU Server: `9.0.16`
- ZFS: `2.3.3-pve1`

## Storage design

The production node uses ZFS-based storage.

- `rpool` — system/root pool
- `vm-storage` — VM storage pool using RAIDZ2

An additional mirrored ZFS pool is used for OS backup where available.

## Virtualization

The node hosts Windows and Linux virtual machines. VM resources are managed through Proxmox VE and monitored through Zabbix.

## Monitoring architecture

```text
Zabbix Server
    |
    +-- Proxmox VE API
    |      +-- node discovery
    |      +-- VM discovery
    |      +-- storage metrics
    |
    +-- Zabbix Agent2
           +-- host CPU/memory/network
           +-- custom ZFS health checks
```

## Design goals

- resilient storage
- centralized monitoring
- clean separation between OS and VM storage
- reproducible configuration documentation
- secure handling of API credentials and secrets
