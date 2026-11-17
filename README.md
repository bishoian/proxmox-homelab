# Proxmox Homelab

**Owner:** Ian Bishop  
**Status:** Active build and documentation project

A small self-hosted infrastructure lab built to develop practical skills in virtualization, Linux administration, storage, Docker, networking, and service operations.

The lab runs on a Lenovo ThinkCentre M920q Tiny with Proxmox VE. A Debian VM hosts the media stack, while a TerraMaster D4-320 provides USB-attached storage.

## Current environment

- **Hypervisor:** Proxmox VE 9.2, running kernel `7.0.14-29-pve` and `pve-manager 9.2.27`.
- **Compute:** Lenovo ThinkCentre M920q Tiny, Intel Core i5-8500, 16 GB RAM, 256 GB NVMe.
- **Media VM:** VM 100, Debian 13.7, four vCPUs, 4 GiB RAM, 64 GiB system disk on NVMe-backed `local-lvm`.
- **Bulk storage:** One 6 TB Seagate Exos disk in a TerraMaster D4-320, passed through to Debian and formatted as ext4 at `/srv/media`.
- **Networking:** RealHD 8-port 2.5GbE managed switch; the M920q's onboard Ethernet currently negotiates at 1 Gbps.
- **Containers:** Jellyfin, Radarr, Sonarr, Prowlarr, Gluetun, qBittorrent, and Seerr are deployed. Mealie and Audiobookshelf are defined in Compose but not yet deployed.
- **Remote access:** Nginx Proxy Manager provides verified HTTPS access to Jellyfin and Seerr. Public hostnames are intentionally masked in this repository.

## Verified work

- Installed and updated Proxmox VE; verified the active kernel and package versions after reboot.
- Built a Debian 13 VM, configured SSH and QEMU Guest Agent, and verified host-to-guest agent communication.
- Passed the Seagate disk through to Debian, created an ext4 filesystem, configured a UUID-based mount, and validated container write access using UID/GID 1000.
- Deployed the Docker media stack and configured connections between Prowlarr, Radarr, Sonarr, qBittorrent, Seerr, and Jellyfin.
- Verified Radarr and Sonarr import and sort media correctly with the expected metadata.
- Verified Jellyfin automatically refreshes its library and plays newly downloaded media.
- Verified Seerr through its external HTTPS request endpoint.
- Routed qBittorrent through Gluetun and ExpressVPN. A direct-IP test confirmed outbound traffic is blocked when the VPN interface is down. The stack recovered after a full Debian VM reboot.
- Diagnosed and corrected Docker DNS failures affecting the media applications and Nginx Proxy Manager.
- Corrected NPM's Docker network connectivity and made its media-network attachment persistent.

## Current work and known gaps

- Deploy and configure Mealie and Audiobookshelf.
- Verify hardlink behavior between download and library paths.
- Confirm the Seagate mount source after a VM reboot.
- Complete an extended SMART test. The first test was stopped at 20% and is recorded as incomplete, not passed.
- Establish independent backups and test a restore.
- Add service monitoring and document routine maintenance.
- Select and deploy Dynamic DNS and Pi-hole.
- Map switch ports, trace rack cabling, and plan VLAN segmentation.

## Hardware

| Component | Configuration |
|---|---|
| Compute | Lenovo ThinkCentre M920q Tiny, i5-8500, 16 GB RAM, 256 GB NVMe |
| DAS | TerraMaster D4-320, four-bay USB enclosure |
| Data disk | 1 × Seagate Exos ST6000NM0115, 6 TB; second drive reserved for later |
| Router | NETGEAR Nighthawk RAX41v2 |
| Switch | RealHD, 8 × 2.5GbE and 1 × 10G SFP+ |
| Rack | GeeekPi DeskPi RackMate T1 Plus, 8U, 10-inch |

The D4-320 is direct-attached storage, not a NAS. The first disk is dedicated to the Debian VM; no RAID or pooled storage is configured.

## Architecture

```text
Internet
   |
NETGEAR Nighthawk RAX41v2
   |
RealHD managed switch
   |
Lenovo ThinkCentre M920q
   |
Proxmox VE
   |
Debian 13 VM (media-server)
   |                         \
Docker media stack            Nginx Proxy Manager
   |                         |
Seagate ext4 storage          HTTPS reverse proxy
   |
TerraMaster D4-320 (USB)
```

The D4-320 connects directly to the Lenovo over USB, not through the Ethernet switch. Detailed topology and the current port map are in [Network Architecture](docs/network-architecture.md).

## Repository guide

| Path | Contents |
|---|---|
| [Project Status](docs/project-status.md) | Current checklist and dated verification log |
| [Hardware Inventory](docs/hardware-inventory.md) | Equipment, specifications, and commissioning state |
| [Network Architecture](docs/network-architecture.md) | Physical topology, service paths, and network roadmap |
| [Package Repositories](docs/package-repositories.md) | Proxmox and Debian package sources |
| [Proxmox Installation](virtualization/proxmox-installation.md) | Hypervisor installation choices |
| [Proxmox Host Baseline](virtualization/proxmox-host-baseline.md) | Host hardware, storage, networking, and updates |
| [Debian Media VM](virtualization/debian-media-server.md) | VM build, guest configuration, and storage passthrough |
| [Media Storage Design](storage/storage-layout.md) | Filesystem, directory layout, and container paths |
| [Seagate SMART Baseline](storage/seagate-smart-baseline.md) | Drive health observations and test history |
| [Media Stack](services/media-stack/README.md) | Compose services, ports, storage, and VPN design |
| [Reverse Proxy](services/reverse-proxy/README.md) | Nginx Proxy Manager configuration and validation |
| [DNS Services](services/dns/README.md) | Planned Dynamic DNS and Pi-hole |
| [D4-320 Drive Not Detected](troubleshooting/d4-320-drive-not-detected.md) | Enclosure power-state diagnosis |
| [Docker DNS and Reverse Proxy](troubleshooting/docker-dns-and-reverse-proxy.md) | Container DNS, Docker network, and NPM troubleshooting |

## Documentation conventions

Documentation distinguishes verified configuration from planned work. Troubleshooting records focus on the symptom, evidence, root cause, fix, and validation. Secrets, private addresses, disk serials, filesystem UUIDs, and usable public hostnames are not published.

No open-source license has been selected. Public visibility does not grant permission to reuse the repository's contents.
