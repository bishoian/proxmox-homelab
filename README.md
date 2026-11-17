# Proxmox Homelab

**Project owner: Ian Bishop**

A hands-on homelab project focused on virtualization, Linux administration, networking, storage, and self-hosted services. The goal is to build and maintain a practical infrastructure environment that develops real-world systems administration skills.

The lab is built around a **Lenovo ThinkCentre M920q Tiny** and is growing from a single-node Proxmox VE host into a small, versatile infrastructure environment.

> **Current status:** Proxmox VE 9.2 is installed on the Lenovo's internal NVMe and updated to kernel `7.0.14-29-pve` with `pve-manager` `9.2.27`. The TerraMaster D4-320 is connected over USB with one 6 TB Seagate Exos drive installed. Its initial SMART overall status was `PASSED`; the extended self-test was stopped early with 80% remaining and is documented as incomplete. VM 100 (`media-server`) runs Debian 13.7 with a 64 GiB NVMe-backed system disk and the Seagate passed through as SCSI1. Debian has an ext4 filesystem mounted at `/srv/media`, with the shared media/download directory structure and UID/GID 1000 ownership. A full Debian VM reboot was completed and the Docker/VPN stack recovered; an explicit post-reboot mount-source check remains on the storage checklist.
>
> The Docker Compose stack includes Jellyfin, Radarr, Sonarr, Prowlarr, Gluetun, qBittorrent, and Seerr. The ExpressVPN tunnel is provided by Gluetun; qBittorrent shares its network namespace, binds to `tun0`, and passed a direct-IP kill-switch test while the VPN interface was down. Gluetun and qBittorrent recovered after a full VM reboot. Radarr, Sonarr, and Prowlarr use explicit DNS resolvers after Docker's embedded DNS forwarding path caused application lookup failures. Seerr is deployed and works locally, with Jellyfin libraries and Radarr/Sonarr configured.
>
> Radarr and Sonarr are verified working, correctly importing and sorting media with the expected metadata. Jellyfin's automatic library refresh and playback have also been verified with a movie watched from a client. Seerr's verification is complete, including its external request endpoint. Hardlink/inode behavior, backups, monitoring, Dynamic DNS, and Pi-hole remain separate follow-up items.
>
> Nginx Proxy Manager provides HTTPS for Jellyfin at [https://m*******.com](https://m*******.com). Its media-network attachment is persistent, and an explicit DNS resolver override fixed NPM's inability to resolve the Let's Encrypt API. Seerr's intended public endpoint is `requests.m*******.com`; its final certificate and external access still need verification. Mealie and Audiobookshelf service definitions have been added to the version-controlled Compose file, but their containers are not yet deployed. Dynamic DNS, Pi-hole, monitoring, and independent backups remain planned. See [Media Stack Notes](services/media-stack/README.md), [Project Status](docs/project-status.md), [Network Architecture](docs/network-architecture.md), [Reverse Proxy Notes](services/reverse-proxy/README.md), [DNS Services Plan](services/dns/README.md), [Installation Notes](virtualization/proxmox-installation.md), [Host Baseline](virtualization/proxmox-host-baseline.md), [Hardware Inventory](docs/hardware-inventory.md), and the [Media Storage Design](storage/storage-layout.md).

## Project goals

- Administer a Proxmox VE virtualization host.
- Deploy and maintain Debian virtual machines and Linux containers.
- Host and maintain self-managed applications, including Jellyfin, Seerr, Mealie, Audiobookshelf, and supporting media services.
- Practice direct-attached storage administration, disk health monitoring, filesystem management, and backups.
- Learn managed switching, network documentation, and VLAN concepts.
- Build a Windows Server and Active Directory practice environment.
- Publish selected self-hosted services through a reverse proxy and custom domain with HTTPS.
- Maintain domain reachability across public IP changes with dynamic DNS.
- Add Pi-hole for LAN DNS filtering and local name resolution.
- Route qBittorrent through a VPN-backed network namespace with a fail-closed kill switch.
- Develop repeatable troubleshooting, maintenance, and recovery procedures.
- Maintain clear documentation that demonstrates practical IT skills.

## Public access and DNS

Jellyfin is reachable through Nginx Proxy Manager (NPM) at the externally hosted endpoint [https://m*******.com](https://m*******.com), with a Let's Encrypt certificate. NPM listens on TCP ports 80 and 443 for web traffic; its administration interface uses port 81 and is intended to remain LAN-only. NPM forwards to Jellyfin over the Docker network using `jellyfin:8096`. The NPM Compose project declares `media-stack_default` as an external network alongside its own default network. HTTPS access was verified after container recreation, and Jellyfin's automatic library refresh and playback were subsequently confirmed.

Seerr is deployed and configured with Jellyfin, Radarr, and Sonarr. Its externally verified request endpoint is `requests.m*******.com`, proxied by NPM to `seerr:5055`. NPM's separate Compose service also uses explicit Cloudflare and Google DNS resolvers after its container could not resolve the Let's Encrypt API through Docker's embedded resolver.

Dynamic DNS is planned so the domain's DNS record can follow the home's public IP address if it changes. Pi-hole is planned as a LAN-only DNS service for network-level filtering and local DNS records. The DNS provider and update method, and whether the router or Pi-hole will provide DHCP, remain to be selected.

qBittorrent now shares Gluetun's network namespace and uses ExpressVPN through OpenVPN. qBittorrent is bound to `tun0`; a direct-IP test to `1.1.1.1` timed out while the VPN interface was down, confirming fail-closed outbound blocking. Dynamic DNS and Pi-hole remain planned. A provider with VPN port forwarding may be evaluated later if inbound peer connectivity becomes a priority.

## Hardware at a glance

| Component | Configuration | Current status |
|---|---|---|
| Compute | Lenovo ThinkCentre M920q Tiny, Intel Core i5-8500, 16 GB RAM, 256 GB NVMe | Proxmox VE 9.2.2 installed; web interface accessible |
| Storage enclosure | TerraMaster D4-320, four-bay USB DAS | Connected to Lenovo; installed drive detected |
| Data disks | 2 × Seagate Exos ST6000NM0115, 6 TB each | One 5.5 TiB disk passed initial SMART overall check and is configured for Debian VM 100; ext4 filesystem created; second awaiting delivery |
| Network switch | RealHD 8-port 2.5GbE web-managed switch with 1 × 10G SFP+ port | Installed in rack |
| Router | NETGEAR Nighthawk RAX41v2 | Existing router |
| Rack | GeeekPi DeskPi RackMate T1 Plus, 8U, 10-inch, 260 mm depth | Assembled |
| Rack power | GeeekPi 1U PDU, six outlets, surge protection, 14 AWG | Installed in rack |
| Patch panel | GeeekPi 12-port, 0.5U CAT6, 10-inch | Installed in rack |
| Mini PC shelf | GeeekPi 10-inch 1U shelf with RJ45/CAT6 and HDMI pass-through ports | Installed in rack |
| Ventilated shelves | 2 × 0.5U and 2 × 1U, 7.87-inch depth | Installed in rack |
| Patch cables | 8 × 0.5 ft CAT6A | Installed in rack |
| Display cables | 2 × 8K HDMI 2.1 cables | Available |

See [Hardware Inventory](docs/hardware-inventory.md) for the complete component list and installation notes.

## Target architecture

```text
                         Internet
                            |
                   NETGEAR Nighthawk
                        RAX41v2
                            |
                 RealHD managed switch
                            |
                 Lenovo ThinkCentre
                       M920q
                            |
                      Proxmox VE 9.2.2
                       /          \
                Virtual machines  USB
                 and containers    |
                            TerraMaster D4-320
                                      |
                             Seagate Exos SATA HDD
                                6 TB installed
```

The TerraMaster D4-320 is **direct-attached storage (DAS)**. It connects to the Lenovo over USB rather than to the Ethernet switch. The enclosure is connected, and the installed drive is visible to Proxmox. Its overall SMART status reports `PASSED`; the initial health attributes were reviewed, while the extended self-test was stopped early and remains incomplete.

The diagram shows the target network layout. Switch cabling, port assignments, and VLAN configuration will be documented as they are confirmed. Planned virtual machines and services are not considered deployed until they have been configured and tested.

## Project milestones

1. **Preparation and installation**
   - [x] Create bootable Proxmox installer USB.
   - [x] Enable Intel Virtualization Technology (VT-x) and Intel VT-d in BIOS.
   - [x] Boot from the USB and reach the Proxmox installer.
   - [x] Install Proxmox VE to the Lenovo's internal NVMe and reboot to the local console.
   - [x] Log in to the web interface and confirm the `pve` node is online.
   - [x] Confirm Proxmox VE version 9.2.2.
   - [x] Verify CPU model, CPU count, and usable memory.
   - [x] Confirm the onboard Ethernet link negotiates at 1 Gbps.
   - [x] Record the NVMe model and partition layout.
2. **Foundational administration**
   - [x] Configure Proxmox VE and Ceph no-subscription repositories.
   - [x] Refresh package lists successfully.
   - [x] Review and apply available package upgrades.
   - [x] Reboot to activate the updated kernel and verify the running kernel and Proxmox package versions.
   - [ ] Configure time and administrative settings.
   - [x] Create VM 100 (`media-server`) with the planned CPU, memory, NVMe disk, and VirtIO network configuration.
   - [x] Complete Debian 13.7 base installation and install GRUB to the VM's virtual disk.
   - [x] Detach the installer ISO, reboot, and verify the first Debian boot.
   - [ ] Establish and test a backup approach before relying on hosted services.
3. **Storage and media services**
   - [x] Connect the D4-320 to the Proxmox host.
   - [x] Verify disk detection and confirm SMART overall status is `PASSED`.
   - [x] Review the initial SMART attributes and record the reported power-on counters.
   - [x] Stop the extended SMART self-test early and record its incomplete result.
   - [ ] Consider a completed extended test during a later maintenance window.
   - [x] Configure the Seagate ext4 filesystem, UUID-based mount at `/srv/media`, and shared media/download directory layout.
   - [x] Deploy Jellyfin and configure its read-only media bind mount.
4. **Application hosting and monitoring**
   - [x] Deploy Docker workloads in the Debian VM, including Jellyfin, Radarr, Sonarr, Prowlarr, Gluetun, and qBittorrent.
   - [x] Deploy and configure Seerr locally with Jellyfin, Radarr, and Sonarr.
   - [ ] Deploy Mealie and Audiobookshelf; their Compose definitions are committed, but container startup and first-run setup remain.
   - [ ] Add service monitoring and alerting.
5. **IT administration lab**
   - [ ] Build Windows Server and a Windows client VM.
   - [ ] Practice Active Directory, DNS, DHCP, and Group Policy in an isolated lab.
6. **Networking**
   - [ ] Document the current LAN and switch configuration, including the physical uplink path.
   - [ ] Plan and test VLAN segmentation after the baseline network is stable.

## Repository guide

- [Hardware Inventory](docs/hardware-inventory.md) — equipment, specifications, and installation status.
- [Network Architecture](docs/network-architecture.md) — current and target network design.
- [Project Status](docs/project-status.md) — milestone checklist and verification log.
- [Proxmox Installation Notes](virtualization/proxmox-installation.md) — installer choices and first-boot process.
- [Proxmox Host Baseline](virtualization/proxmox-host-baseline.md) — first web-interface login and initial host observations.
- [Package Repository Configuration](docs/package-repositories.md) — Proxmox VE, Ceph, and Debian package sources.
- `virtualization/` — Proxmox host, VM, and container build records.
- [Media Stack Compose](services/media-stack/compose.yaml) — Jellyfin, Radarr, Sonarr, Prowlarr, Gluetun, qBittorrent, Seerr, Mealie, and Audiobookshelf service definitions.
- [Media Stack Notes](services/media-stack/README.md) — service ports, storage mappings, deployment notes, and the deployed Gluetun VPN gateway.
- [Reverse Proxy Notes](services/reverse-proxy/README.md) — Nginx Proxy Manager, the `m*******.com` Jellyfin endpoint, and the persistent Docker network plan.
- [DNS Services Plan](services/dns/README.md) — planned Dynamic DNS and Pi-hole implementation.
- `services/` — deployment notes and configuration examples for hosted applications.
- [D4-320 Drive Not Detected](troubleshooting/d4-320-drive-not-detected.md) — power-state diagnosis and storage detection verification.
- [Seagate SMART Baseline](storage/seagate-smart-baseline.md) — initial health attributes and planned self-test.
- [Media Storage Design](storage/storage-layout.md) — single-disk layout for the Debian media-services VM.
- `troubleshooting/` — incidents, diagnosis, fixes, and verification.

## Documentation approach

Each major implementation includes its purpose, environment, configuration, validation results, any issues encountered, and lessons learned. Planned work remains clearly identified until it has been completed and tested.

## License

No license has been selected. The repository is publicly viewable on GitHub, but no explicit open-source reuse license has been granted.
