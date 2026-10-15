# Backup and Recovery

## Captured state

The live export did not contain any configured jobs in `/etc/pve/jobs.cfg` at the time of capture.

That means no Proxmox backup job was visible in this export. This does not prove that no external backup process exists, but no scheduled PVE job was present in the captured configuration.

## Recommended backup scope

Protect both guest workloads and the Proxmox host configuration needed to rebuild the environment.

For each critical VM, document:

- VMID
- backup target
- schedule
- retention
- last successful backup
- restore test date

## VM backup checks

```bash
vzdump --help
pvesm status
cat /etc/pve/jobs.cfg
```

VM backups should be stored outside the primary VM storage whenever possible.

## Host configuration backup

Important configuration areas include:

```text
/etc/pve/
/etc/network/interfaces
/etc/zabbix/zabbix_agent2.conf
/etc/zabbix/zabbix_agent2.d/
```

Never publish raw `/etc/pve/priv/` contents or any secret-bearing files.

## ZFS validation

Before storage maintenance:

```bash
zpool status
zpool list
zfs list
```

At the time of capture both `rpool` and `vm-storage` were ONLINE and their latest scrub completed with zero errors.

## Recovery priorities

1. Restore management connectivity.
2. Validate ZFS pools.
3. Restore Proxmox host configuration.
4. Restore critical VMs.
5. Validate networking and guest services.
6. Re-enable and validate monitoring.

## Restore testing

A backup is not considered reliable until a restore has been tested. Record restore tests, recovery time and any manual dependencies discovered during testing.
