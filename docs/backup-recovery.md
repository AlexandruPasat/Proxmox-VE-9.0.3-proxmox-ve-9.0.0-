# Backup and Recovery

## Objective

Backups should protect both guest workloads and the Proxmox host configuration needed to rebuild the environment.

## VM backups

VM backups should be stored outside the primary VM storage whenever possible.

Useful checks:

```bash
vzdump --help
pvesm status
```

For each critical VM, document:

- VMID
- backup target
- schedule
- retention
- last successful backup
- restore test date

## Host configuration backup

Important configuration areas include:

```text
/etc/pve/
/etc/network/interfaces
/etc/zabbix/zabbix_agent2.conf
/etc/zabbix/zabbix_agent2.d/
```

Do not publish raw `/etc/pve/priv/` content or any secret-bearing files.

## ZFS

Before storage maintenance:

```bash
zpool status
zpool list
zfs list
```

Validate backups before replacing disks or modifying vdev topology.

## Recovery priorities

1. Restore management connectivity.
2. Validate storage pools.
3. Restore Proxmox configuration.
4. Restore critical VMs.
5. Validate networking and services.
6. Re-enable monitoring.

## Restore validation

A backup is not considered reliable until a restore has been tested. Record restore tests and any issues encountered.
