# Networking

## Management

- Proxmox node: `proxmox`
- Management IP: `10.100.184.5`
- Web interface: `https://10.100.184.5:8006`

## Bridge model

Proxmox VE uses Linux bridges to connect virtual machines to physical networks. VM NICs are attached to a bridge such as `vmbr0`, which in turn is bound to a physical interface or bond.

## Validation commands

```bash
ip addr
ip route
ip link
bridge link
bridge vlan
```

To inspect Proxmox network configuration:

```bash
cat /etc/network/interfaces
```

## VM connectivity troubleshooting

Recommended order:

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

Do not commit live public IPs, VPN secrets, private keys or firewall credentials. Sanitized network examples may be stored under `configs/`.
