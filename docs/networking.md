# Networking

## Live network design

The Proxmox host uses a Linux bridge for management and VM connectivity.

```text
eno1
  |
  v
vmbr0
  |
  +-- Proxmox management
  +-- VM interfaces
```

## Interfaces

Captured state:

- `eno1` — active physical uplink, configured as `manual`
- `eno2` — unused/manual
- `eno3` — unused/manual
- `eno4` — unused/manual
- `vmbr0` — active static bridge

Bridge settings:

```text
bridge-ports eno1
bridge-stp off
bridge-fd 0
```

The management address and default gateway are configured on `vmbr0`.

Exact production IP addresses are intentionally redacted from this public repository.

## VM networking

VM tap interfaces are attached through Proxmox firewall bridges where enabled. The captured environment shows interfaces such as:

```text
tap100i0
fwbr100i0
fwpr100p0
fwln100i0
```

This is normal when the Proxmox firewall path is active for a VM NIC.

## VLAN state

The captured bridge state showed PVID 1 / untagged forwarding on the active bridge path. No additional VLAN-aware bridge configuration was present in the exported `/etc/network/interfaces`.

## Validation commands

```bash
ip -br addr
ip route
bridge link
bridge vlan show
cat /etc/network/interfaces
```

## Troubleshooting sequence

```text
Interface/IP
  -> Route
  -> Neighbor/ARP
  -> Ping
  -> TCP port
  -> Firewall
  -> Service
  -> Application
```

Useful commands:

```bash
ip addr
ip route get <destination>
ip neigh
ping <destination>
nc -vz <destination> <port>
ss -lntup
nft list ruleset
tcpdump -ni <interface>
```

## Security

Do not commit live IP addresses, VPN secrets, private keys or firewall credentials. Public documentation should describe topology and behavior while redacting sensitive addressing information.
