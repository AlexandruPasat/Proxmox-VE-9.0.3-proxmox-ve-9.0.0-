# VM Setup

## Current virtual machines

The captured production host has six running QEMU virtual machines and no LXC containers.

| VMID | Name | RAM | Boot disk |
|---|---|---:|---:|
| 100 | `Windows-10-Pro-H` | 32 GB | 300 GB |
| 101 | `badge-equipment-pv-webserver` | 4 GB | 32 GB |
| 102 | `AlexLinux` | 4 GB | 32 GB |
| 103 | `Zabbix` | 16 GB | 300 GB |
| 104 | `DataBaseLinuxVM` | 8 GB | 150 GB |
| 105 | `Windows-HyperV` | 32 GB | 250 GB |

## Storage placement

The current environment uses both ZFS pools for VM disks.

- VM 100 is stored on `vm-storage`.
- VMIDs 101-105 have disks present under `rpool/data`.

This is a mixed state. The intended long-term design is to keep the hypervisor/system workload on the SSD mirror while moving VM workloads to dedicated VM storage where appropriate.

## Resource sizing

VM sizing should be based on workload requirements and actual monitoring data. Avoid excessive overcommitment without validating CPU, RAM and storage behavior.

Typical parameters:

- vCPU
- RAM
- disk size
- storage target
- bridge
- NIC model
- BIOS/UEFI firmware

## VM inspection

List VMs:

```bash
qm list
```

Inspect a VM configuration:

```bash
qm config <VMID>
```

Example:

```bash
qm config 100
```

## VM lifecycle

```bash
qm start <VMID>
qm shutdown <VMID>
qm stop <VMID>
qm reboot <VMID>
qm status <VMID>
```

## Networking

VM NICs are attached to `vmbr0` in the captured environment.

Useful validation:

```bash
qm config <VMID> | grep -E 'net|bridge'
```

## Documentation rule

Do not commit guest passwords, cloud-init secrets, API credentials or other sensitive metadata from VM configuration files.
