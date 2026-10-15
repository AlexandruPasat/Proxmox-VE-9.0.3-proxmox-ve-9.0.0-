# Architecture

## Host platform

- Hypervisor: Proxmox VE
- Node: `proxmox`
- Hardware: Dell PowerEdge R740
- Operating system: Debian GNU/Linux 13 (trixie)
- Architecture: `x86_64`
- CPU: Intel Xeon Silver 4208 @ 2.10 GHz
- CPU topology: 1 socket / 8 cores / 16 threads
- Memory: approximately 187 GiB usable RAM
- Swap: disabled

Hardware service tags, UUIDs, disk serials and management IP addresses are intentionally excluded from this public repository.

## Software baseline

| Component | Version |
|---|---|
| Proxmox VE manager | `9.0.3` |
| Proxmox VE meta package | `9.0.0` |
| Kernel | `6.14.8-2-pve` |
| QEMU Server | `9.0.16` |
| ZFS | `2.3.3-pve1` |
| Zabbix Agent2 | `7.0.31` |

## Storage architecture

```text
2 x 480 GB SSD
    └── rpool
        └── mirror-0
            ├── Proxmox root filesystem
            ├── local-zfs / rpool/data
            └── several VM disks

6 x ~600 GB HDD
    └── vm-storage
        └── RAIDZ2
            └── VM storage
```

Both pools were ONLINE in the captured production state and their latest scrub reported zero repaired bytes and zero errors.

### Current placement note

The desired design is to keep the Proxmox system on the SSD mirror and use the RAIDZ2 HDD pool for VM workloads. The current live state is mixed: VM 100 is on `vm-storage`, while multiple other VM disks are still located on `rpool/data`.

This repository documents the live state rather than pretending the target layout is already complete.

## Network architecture

```text
Physical NIC eno1
      |
      v
    vmbr0
      |
      +-- Proxmox management
      +-- virtual machine NICs
```

- `eno1` is the active physical uplink.
- `vmbr0` has the static management configuration.
- STP is disabled on the bridge.
- Bridge forwarding delay is `0`.
- `eno2`, `eno3` and `eno4` are currently unused/manual.

Exact IP addresses are intentionally redacted from the public repository.

## Virtualization

The captured host has six running QEMU virtual machines with Windows and Linux workloads. No LXC containers were present in the export.

## Monitoring architecture

```text
Zabbix Server
    |
    +-- Proxmox VE API
    |      +-- node metrics
    |      +-- VM discovery
    |      +-- storage metrics
    |
    +-- Zabbix Agent2 7.0.31
           +-- host metrics
           +-- ZFS pool health
           +-- ZFS capacity
           +-- ZFS error state
           +-- scrub status
```

## Design goals

- resilient ZFS storage
- centralized monitoring
- controlled migration of VM workloads to dedicated VM storage
- reproducible infrastructure documentation
- secure handling of credentials and production identifiers
