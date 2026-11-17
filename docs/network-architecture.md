# Network Architecture

The home LAN remains a flat network. VLANs and Dynamic DNS are planned, not deployed.

## Physical topology

```text
Internet
   |
NETGEAR Nighthawk RAX41v2
   |
RealHD managed switch
   |-------------------- Other LAN devices
   |
Lenovo ThinkCentre M920q
   |                 \
Proxmox VE            USB
   |                   |
Debian media VM    TerraMaster D4-320
```

The D4-320 is USB direct-attached storage, not a NAS. The patch panel is passive and does not switch or route traffic.

## Current network

- Proxmox management is available from the home LAN.
- The Lenovo's physical interface `nic0` and bridge `vmbr0` are up at 1 Gbps.
- The RealHD switch supports 2.5GbE, but the M920q's onboard NIC is Gigabit Ethernet.
- The physical cable path, switch port assignments, and switch management details still need to be documented.
- No VLANs are configured.

## Public service path

Nginx Proxy Manager (NPM) runs in a separate Compose project on the Debian VM and publishes ports 80, 81, and 443. Jellyfin and Seerr have verified HTTPS access through NPM. Public hostnames are masked in this repository.

NPM shares the `media-stack_default` Docker network with the media applications while retaining its own default network. Port 81 is for LAN administration only and must not be forwarded from the internet.

See [Reverse Proxy](../services/reverse-proxy/README.md) for the current configuration and [Docker DNS and Reverse Proxy Troubleshooting](../troubleshooting/docker-dns-and-reverse-proxy.md) for the original 502 and DNS issues.

## qBittorrent VPN path

qBittorrent shares Gluetun's network namespace and is bound to `tun0`. Gluetun provides the ExpressVPN OpenVPN tunnel and firewall. Radarr and Sonarr connect to qBittorrent through `gluetun:8080`.

The kill switch was tested by bringing `tun0` down. A DNS-based request failed, and a direct-IP HTTPS request to `1.1.1.1` timed out (HTTP 000). Gluetun and qBittorrent recovered after recreation. Following a full Debian VM reboot, Gluetun returned healthy, qBittorrent retained `tun0`, VPN egress was restored, and Radarr/Sonarr again reached the qBittorrent Web UI.

## Planned DNS services

- **Dynamic DNS:** no provider or updater has been selected. The goal is to keep the public DNS record aligned with a changing home WAN address.
- **Pi-hole:** planned for LAN DNS filtering and local DNS records. Decide whether DHCP stays on the router or moves to Pi-hole before enabling DHCP on Pi-hole.

Do not expose Pi-hole's DNS or administration ports publicly.

## Planned VLANs

These are examples for a future segmentation exercise, not configured networks.

| VLAN | Intended use | Status |
|---:|---|---|
| 10 | Infrastructure management | Proposed |
| 20 | Server workloads | Proposed |
| 30 | Trusted personal devices | Proposed |
| 40 | Isolated lab/testing | Proposed |

Before implementing VLANs, verify that the router or firewall supports the required VLAN interfaces, routing, DHCP, and firewall policies. Keep a known-good management path and recovery method available.

## Port map

| Switch port | Connected device | Negotiated speed | VLAN | Verified |
|---|---|---|---|---|
| 1-8 | Not yet mapped | Not recorded | Default LAN | No |
| SFP+ | Unassigned | Not recorded | Not assigned | No |

## Remaining network tasks

- [ ] Trace the router-to-switch uplink and rack patching.
- [ ] Identify switch model/revision, firmware, and management interface.
- [ ] Record switch port assignments and negotiated speeds.
- [ ] Select and test a Dynamic DNS provider/updater.
- [ ] Deploy Pi-hole and decide which device provides DHCP.
- [ ] Design and test VLAN segmentation after the LAN baseline is documented.
