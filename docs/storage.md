# Storage

## Overview

The Proxmox node uses ZFS for resilient storage and VM workloads.

## Pools

### `rpool`

Purpose:

- Proxmox root filesystem
- system datasets

The pool is monitored for health, capacity and ZFS errors.

### `vm-storage`

Purpose:

- primary VM storage

Layout:

- RAIDZ2

RAIDZ2 provides dual-parity protection, allowing the pool to tolerate two disk failures within the vdev before data loss.

### OS backup pool

A mirrored ZFS pool is used for OS backup where configured.

## Operational checks

Useful commands:

```bash
zpool status
zpool list
zfs list
```

Per-pool checks:

```bash
zpool status rpool
zpool status vm-storage
```

## Monitoring

Custom Zabbix checks cover:

- pool health
- capacity
- error state
- scrub status

See `configs/zfs-monitoring.conf` and `docs/zabbix-monitoring.md`.

## Safety

Before changing vdevs, replacing disks or altering pool topology, verify:

- current pool health
- latest backup status
- device identifiers
- controller mapping

Never commit storage encryption keys or backup secrets to this repository.
