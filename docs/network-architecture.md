# Network Architecture

This document outlines the homelab's physical network and the planned logical layout. Port assignments, link speeds, and switch settings will be added as they are confirmed.

## Physical topology

```text
                         Internet
                            |
                    NETGEAR Nighthawk
                         RAX41v2
                            |
                      Ethernet uplink
                            |
                  RealHD managed switch
                   8 x 2.5GbE + 10G SFP+
                     /       |        \
                    /        |         \
          Lenovo M920q   Patch panel   Other LAN
          Proxmox host   / fixed runs   devices
               |
          USB connection
               |
        TerraMaster D4-320
          Direct-attached
             storage
```

The Proxmox host is installed and its web interface is accessible. The D4-320 is physically connected to the Lenovo over USB, and the installed Seagate drive is detected with an initial SMART overall status of `PASSED`. The drive's extended self-test was stopped early and remains incomplete; see the SMART baseline notes.

The patch panel is a passive cabling termination point. It does not switch or route traffic. The relationship between the switch, patch panel, rack shelf pass-through ports, router, and room cabling will be documented after the cables are traced.

## Equipment

| Device | Role | Notes |
|---|---|---|
| NETGEAR Nighthawk RAX41v2 | Home router / internet gateway | Provides the existing home network |
| RealHD 8-port 2.5GbE managed switch | Ethernet switching | Exact model/revision, firmware, management method, and configuration to be verified |
| GeeekPi 12-port CAT6 patch panel | Passive cable organization | No switching or routing functionality |
| Lenovo ThinkCentre M920q | Proxmox host | Onboard `nic0` link verified at 1 Gbps; Proxmox bridge `vmbr0` uses this uplink |
| TerraMaster D4-320 | Direct-attached storage | Connected to Lenovo over USB; not Ethernet |

## Current network

The initial deployment uses the existing home LAN without VLANs or changes to router-wide settings.

The Proxmox host has a static management address reserved in the home router. The web interface is accessible from another device on the LAN.

The host's physical Ethernet interface (`nic0`) is UP at 1000 Mb/s (1 Gbps). The Proxmox bridge (`vmbr0`) is UP and uses the same physical uplink. The wireless interface (`wlo1`) is DOWN; the server is using its wired connection. The current physical path from the Lenovo to the router or managed switch will be traced and documented separately. The host's network configuration and 1 Gbps link have been confirmed from the Proxmox Shell.

## Public service path

The Debian media-services VM runs Docker workloads, including Jellyfin and Nginx Proxy Manager. NPM is deployed as a separate Compose project and publishes TCP ports 80, 81, and 443 on the VM. The custom domain `moxxboxx.com` has a Let's Encrypt certificate and successfully reaches Jellyfin over HTTPS. NPM forwards the request to the Jellyfin service at `http://jellyfin:8096`.

NPM and the media stack initially used separate Docker bridge networks. The NPM container was manually connected to `media-stack_default`, which resolved the 502 Bad Gateway and made the proxy route work. The NPM Compose project now declares `media-stack_default` as an external network while retaining its own default network. The Compose configuration was validated and applied, the NPM container was recreated, and inspection confirmed it remained attached to both networks. A post-recreation request to `https://moxxboxx.com` returned `HTTP/2 302` from OpenResty. The NPM administration interface on port 81 is intended for LAN access only and must not be forwarded from the internet.

## qBittorrent VPN path

qBittorrent shares the Gluetun container's network namespace. Gluetun establishes the ExpressVPN OpenVPN tunnel and provides the firewall kill switch. qBittorrent is bound to the tunnel interface `tun0`; its Web UI is published by Gluetun on TCP 8080. Radarr and Sonarr reach the Web UI through the Gluetun service name on port 8080. A direct-IP HTTPS request from qBittorrent timed out while `tun0` was down, and the VPN stack recovered after container recreation. A full Debian VM reboot was subsequently tested: Gluetun returned healthy, qBittorrent retained the `tun0` interface, VPN egress was restored, and Radarr/Sonarr each reached the qBittorrent Web UI with HTTP 200.

### Planned DNS services

- **Dynamic DNS:** add a provider-compatible updater so the public DNS record for `moxxboxx.com` follows changes to the home's public IP address. DNS provider and updater are not yet selected.
- **Pi-hole:** deploy on the home LAN for DNS filtering and local DNS records. Decide whether the existing router or Pi-hole will provide DHCP before enabling DHCP on Pi-hole. Do not expose Pi-hole's DNS or administration ports publicly.

## Planned segmentation

VLANs are a later learning milestone, not part of the initial installation.

| Example VLAN | Intended use | State |
|---:|---|---|
| 10 | Infrastructure management | Proposed only |
| 20 | Server workloads | Proposed only |
| 30 | Trusted personal devices | Proposed only |
| 40 | Isolated lab/testing | Proposed only |

These VLAN IDs are examples, not configured networks. The router or a dedicated firewall must provide Layer 3 routing, DHCP, and firewall policy for devices in different VLANs. A managed switch alone does not automatically provide those services.

Before implementing VLANs, the router or planned firewall must be checked for the required VLAN interfaces and routing capabilities. A known-good management path and recovery method should remain available during testing.

## Link-speed expectations

The switch supports multi-gigabit ports, but each connection operates at the highest speed supported by both ends of the link and the cabling. The actual negotiated speed will be recorded rather than inferred from the switch's advertised maximum.

The 10G SFP+ port is an uplink option; it does not mean the Lenovo currently has a 10GbE interface.

## Port map

| Switch port | Connected device / destination | Negotiated speed | VLAN | Verified |
|---|---|---|---|---|
| 1 | TBD | TBD | Default LAN | No |
| 2 | TBD | TBD | Default LAN | No |
| 3 | TBD | TBD | Default LAN | No |
| 4 | TBD | TBD | Default LAN | No |
| 5 | TBD | TBD | Default LAN | No |
| 6 | TBD | TBD | Default LAN | No |
| 7 | TBD | TBD | Default LAN | No |
| 8 | TBD | TBD | Default LAN | No |
| SFP+ | TBD / unused | TBD | TBD | No |

## Validation checklist

- [ ] Trace the router-to-switch uplink.
- [ ] Trace switch-to-patch-panel connections.
- [ ] Trace the Lenovo's Ethernet path through the rack shelf/pass-through, if used.
- [x] Confirm the Lenovo's onboard Ethernet link negotiates at 1 Gbps.
- [ ] Trace the physical network path and confirm the switch-side negotiated speed.
- [ ] Identify switch firmware and management access.
- [ ] Record switch port mappings.
- [x] Confirm Proxmox management interface is accessible from a LAN device.
- [ ] Confirm Proxmox detects the D4-320's installed drive.
- [x] Deploy Nginx Proxy Manager and verify HTTPS access to Jellyfin through `moxxboxx.com`.
- [x] Connect NPM to the media stack's Docker network to reach Jellyfin; persistence in the NPM Compose file remains pending.
- [ ] Declare the media stack network as external in NPM's Compose project and verify persistence after container recreation.
- [ ] Select and configure a Dynamic DNS provider/updater.
- [ ] Deploy Pi-hole for LAN DNS filtering and local name resolution.
- [ ] Plan VLANs after the baseline LAN is stable.
