# Zabbix Monitoring

## Overview

The Proxmox node is monitored by Zabbix using both the Proxmox API and Zabbix Agent2.

## API monitoring

Template:

```text
Proxmox VE by HTTP
```

The API integration is used for:

- node discovery
- VM discovery
- VM status
- storage metrics
- host performance data

A dedicated API user/token with read-only privileges should be used.

Do not store the token secret in this repository.

## Zabbix Agent2

Agent2 provides host-level metrics and custom checks.

Typical configuration values:

```text
Server=<ZABBIX_SERVER_IP>
ServerActive=<ZABBIX_SERVER_IP>
Hostname=proxmox
```

## Custom ZFS checks

Custom UserParameters are stored in:

```text
/etc/zabbix/zabbix_agent2.d/zfs.conf
```

The repository copy is available at:

```text
configs/zfs-monitoring.conf
```

Keys:

```text
zfs.health[*]
zfs.errors[*]
zfs.scrub[*]
zfs.capacity[*]
```

## Validation

Restart Agent2 after config changes:

```bash
systemctl restart zabbix-agent2
systemctl status zabbix-agent2
```

Test custom keys locally:

```bash
zabbix_agent2 -t 'zfs.health[rpool]'
zabbix_agent2 -t 'zfs.capacity[rpool]'
```

## Security

Never commit:

- API token secrets
- Zabbix PSK secrets
- SSH private keys
- passwords
