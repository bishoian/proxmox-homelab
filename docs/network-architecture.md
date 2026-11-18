# Network Architecture

I currently run a flat home LAN. I have not deployed VLANs or Dynamic DNS yet; both are part of my planned network improvements.

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

I connect the D4-320 directly to the Lenovo over USB, so I treat it as direct-attached storage rather than a NAS. My patch panel is passive and does not switch or route traffic.

## Current network

- I access Proxmox management from my home LAN.
- My Lenovo's physical interface `nic0` and bridge `vmbr0` are up at 1 Gbps.
- My RealHD switch supports 2.5GbE, but the M920q's onboard NIC is limited to Gigabit Ethernet.
- I still need to trace my physical cable paths, identify switch port assignments, and document switch management details.
- No VLANs are configured.

## Public service path

I run Nginx Proxy Manager (NPM) in a separate Compose project on my Debian VM, publishing ports 80, 81, and 443. I have verified HTTPS access to Jellyfin and Seerr through NPM. I mask public hostnames in this repository.

I connected NPM to the `media-stack_default` Docker network while retaining its own default network. I reserve port 81 for LAN administration and do not forward it from the internet.

See [Reverse Proxy](../services/reverse-proxy/README.md) for the current configuration and [Docker DNS and Reverse Proxy Troubleshooting](../troubleshooting/docker-dns-and-reverse-proxy.md) for the original 502 and DNS issues.

## Remote household applications

I deployed Mealie and Audiobookshelf and verified that both respond over the existing `media-stack_default` Docker network. I plan to provide remote HTTPS access through Nginx Proxy Manager using separate subdomains, with the public names masked in this repository.

- Mealie upstream: `http://mealie:9000`. I will set its `BASE_URL` to its final HTTPS hostname and keep open registration disabled.
- Audiobookshelf upstream: `http://audiobookshelf:80`. I will enable WebSocket Support in NPM for its proxy host.
- Public entry point: NPM on TCP 443, with the existing HTTP/ACME entry on TCP 80.
- DNS: each public subdomain must resolve to my home WAN address. Dynamic DNS is not configured yet, so I must keep the records updated if the address changes.
- Security: I will use each application's own accounts, create a separate account for my girlfriend, and keep admin interfaces private. I will not expose SMB, NPM administration, or media-management ports to the internet.

I removed direct host-port publications for Mealie and Audiobookshelf in Compose. NPM will be their intended access path. Their NPM proxy hosts, certificate issuance, and external-network testing remain outstanding.

## LAN file sharing

I run Samba in the media-stack Compose project on my Debian VM. It shares the separate filesystem mounted at `/srv/shared` as `Shared` over TCP 445. I bind the published port to the Debian VM's LAN interface rather than exposing it through the reverse proxy or VPN container. I do not forward SMB from the WAN.

I require individual Samba credentials and use a shared Linux group for read/write access. I verified local SMB operations for both configured accounts and connected my Windows PC to the share. I have not tested the second household PC yet. I omit the private LAN address and account credentials from this repository.

## qBittorrent VPN path

qBittorrent shares Gluetun's network namespace and is bound to `tun0`. Gluetun provides the ExpressVPN OpenVPN tunnel and firewall. Radarr and Sonarr connect to qBittorrent through `gluetun:8080`.

The kill switch was tested by bringing `tun0` down. A DNS-based request failed, and a direct-IP HTTPS request to `1.1.1.1` timed out (HTTP 000). Gluetun and qBittorrent recovered after recreation. Following a full Debian VM reboot, Gluetun returned healthy, qBittorrent retained `tun0`, VPN egress was restored, and Radarr/Sonarr again reached the qBittorrent Web UI.

## Planned DNS services

- **Dynamic DNS:** no provider or updater has been selected. The goal is to keep the public DNS record aligned with a changing home WAN address.
- **Pi-hole:** planned for LAN DNS filtering and local DNS records. Decide whether DHCP stays on the router or moves to Pi-hole before enabling DHCP on Pi-hole.

I intend to keep Pi-hole's DNS service and administration interface private to my LAN. I will not expose either directly to the internet through WAN port forwarding.

## Planned VLANs

These are examples for a future segmentation exercise, not configured networks.

| VLAN | Intended use | Status |
|---:|---|---|
| 10 | Infrastructure management | Proposed |
| 20 | Server workloads | Proposed |
| 30 | Trusted personal devices | Proposed |
| 40 | Isolated lab/testing | Proposed |

My VLAN design depends on my router or firewall supporting VLAN interfaces, inter-VLAN routing, DHCP, and the firewall policies I need. Before I move devices onto separate VLANs, I will confirm those capabilities and test a recovery path that preserves access to Proxmox management.

## Port map

| Switch port | Connected device | Negotiated speed | VLAN | Verified |
|---|---|---|---|---|
| 1-8 | Not yet mapped | Not recorded | Default LAN | No |
| SFP+ | Unassigned | Not recorded | Not assigned | No |

## Remaining network tasks

- [ ] I will verify both storage mounts, SMB access, and automatic reconnection after a Debian VM reboot.
- [ ] I will test the shared folder from my girlfriend's PC after she moves in.
- [ ] I will trace the router-to-switch uplink and rack patching.
- [ ] I will identify the switch model, hardware revision, firmware, and management interface.
- [ ] I will record switch port assignments and negotiated link speeds.
- [ ] I will select and test a Dynamic DNS provider and updater.
- [ ] I plan to deploy Pi-hole after deciding whether my router or Pi-hole will provide DHCP.
- [ ] After documenting my current LAN, I plan to design and test VLAN segmentation.
