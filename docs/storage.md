# Storage

## Live storage layout

The production node uses two ZFS pools.

## `rpool`

Purpose:

- Proxmox operating system
- root filesystem
- local ZFS storage (`rpool/data`)
- some VM disks

Layout:

```text
rpool
└── mirror-0
    ├── SSD 1 (~480 GB)
    └── SSD 2 (~480 GB)
```

Captured status:

- pool state: `ONLINE`
- approximate pool size: `444G`
- approximate allocation: `223G`
- latest scrub: `0B` repaired, `0` errors

The system root dataset is `rpool/ROOT/pve-1`.

## `vm-storage`

Purpose:

- dedicated VM storage

Layout:

```text
vm-storage
└── raidz2-0
    ├── disk 1
    ├── disk 2
    ├── disk 3
    ├── disk 4
    ├── disk 5
    └── disk 6
```

The six data disks are approximately 600 GB each.

Captured status:

- pool state: `ONLINE`
- approximate raw pool size: `3.27T`
- low fragmentation in the captured state
- latest scrub: `0B` repaired, `0` errors

RAIDZ2 provides dual-parity protection and can tolerate two disk failures within the vdev before data loss.

## Proxmox storage definitions

The captured configuration defines:

```text
local
  type: directory
  path: /var/lib/vz
  content: ISO, backup, container templates

local-zfs
  type: ZFS
  pool: rpool/data
  content: VM images, rootdir

vm-storage
  type: ZFS
  pool: vm-storage
  mountpoint: /vm-storage
  content: VM images, rootdir
```

## Current VM disk placement

The live export shows a mixed storage state.

- VM 100 has its main disk on `vm-storage`.
- Several VM disks for VMIDs 101-105 are still present under `rpool/data`.

This means the target separation of OS-on-SSD and VM-workloads-on-HDD is not yet complete.

## Operational checks

```bash
zpool status
zpool list
zfs list
pvesm status
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

## Security

Disk serial numbers and other unique hardware identifiers from the live export are intentionally omitted from this public repository.
