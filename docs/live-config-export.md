# Live Configuration Export

Use this checklist to collect current Proxmox configuration for documentation without publishing secrets.

## Safe collection commands

Run as root on the Proxmox node:

```bash
pveversion -v
zpool status
zpool list
zfs list
pvesm status
qm list
ip addr
ip route
cat /etc/network/interfaces
cat /etc/pve/storage.cfg
```

For specific VM configuration:

```bash
qm config <VMID>
```

## Review before commit

Before adding live output to GitHub, remove or replace:

- public IP addresses if sensitive
- API token IDs when not needed
- API token secrets
- passwords
- SSH keys
- private certificate material
- backup encryption keys
- `/etc/pve/priv/` contents
- any embedded credentials

## Never export to Git

Do not add raw copies of:

```text
/etc/pve/priv/
/root/.ssh/
/etc/ssh/ssh_host_*_key
```

Use placeholders such as:

```text
<PROXMOX_MGMT_IP>
<API_TOKEN_ID>
<API_TOKEN_SECRET>
<PRIVATE_NETWORK>
```

The purpose of this repository is reproducible documentation, not credential backup.
