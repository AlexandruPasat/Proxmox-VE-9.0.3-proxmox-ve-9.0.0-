# VM Setup

## Current workload pattern

The node hosts production/test virtual machines including Windows and Linux guests.

Known example:

- VMID `100`
- Windows 10 Pro

## Resource sizing

VM sizing should be based on workload requirements and host capacity. Avoid excessive overcommitment without monitoring.

Typical parameters:

- vCPU
- RAM
- disk size
- storage target
- bridge
- NIC model
- firmware (BIOS/UEFI)

## VM configuration inspection

List VMs:

```bash
qm list
```

Show a VM config:

```bash
qm config <VMID>
```

Example:

```bash
qm config 100
```

## VM lifecycle

Common commands:

```bash
qm start <VMID>
qm shutdown <VMID>
qm stop <VMID>
qm reboot <VMID>
qm status <VMID>
```

## Networking

VM NICs are normally connected to a Linux bridge such as `vmbr0`.

Useful validation:

```bash
qm config <VMID> | grep -E 'net|bridge'
```

## Documentation rule

When exporting VM configs to Git, review and sanitize any potentially sensitive metadata before commit.
