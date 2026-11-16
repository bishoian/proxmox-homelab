# Proxmox Homelab

**Project owner: Ian Bishop**

A hands-on homelab project focused on virtualization, Linux administration, networking, storage, and self-hosted services. The goal is to build and maintain a practical infrastructure environment that develops real-world systems administration skills.

The lab is built around a **Lenovo ThinkCentre M920q Tiny** and is growing from a single-node Proxmox VE host into a small, versatile infrastructure environment.

> **Current status:** Proxmox VE 9.2 is installed on the Lenovo's internal NVMe drive, and the `pve` node is online in the web interface. After package updates and reboot, the host is running kernel `7.0.14-29-pve` with `pve-manager` `9.2.27`. The TerraMaster D4-320 is connected over USB, and its installed 6 TB Seagate Exos drive is detected by Proxmox as `/dev/sda`. The disk's overall SMART status is `PASSED`. The initial attribute review shows zero reallocated, pending, offline-uncorrectable, and UDMA CRC error counts, with a reported temperature of 24°C. The extended self-test was intentionally stopped early; its log records Aborted by host with 80% remaining. Only the initial SMART snapshot is available for the first media-storage setup, so the drive will be monitored and important data backed up. The host reports an Intel Core i5-8500, six CPUs, and 15.46 GiB of usable RAM. The onboard Ethernet link (`nic0`) is currently negotiating at 1 Gbps. The Proxmox VE and Ceph Tentacle no-subscription repositories are enabled alongside Debian Trixie and Debian Security. Package upgrades have been applied successfully, and the Updates view now reports no available updates. The updated Proxmox kernel is active following a successful reboot. VM 100 (`media-server`) has been created with four vCPUs, 4 GiB RAM, a 64 GiB NVMe-backed system disk, VirtIO networking, and the Debian 13.7 installer attached. Debian 13.7 installation has completed in VM 100, including GRUB installation to the VM's virtual disk. Debian 13.7 has booted successfully, and the `ian` account login is verified. The guest hostname is `media-server`, and its VirtIO interface (`ens18`) is up with an IPv4 address assigned. DNS resolution is successful, the SSH service is active, and sudo authentication works. A Debian APT update successfully reached the Debian security, base, and updates repositories; APT reported all packages up to date. QEMU Guest Agent is installed and reports active inside Debian. Proxmox host-to-guest communication has been verified with `qm agent 100 ping`, which completed successfully without output. Remote SSH access from Windows PowerShell is verified; the Windows client accepted the Debian host key and authenticated as `ian`. The Seagate Exos was identified on the Proxmox host as a separate USB disk and attached to VM 100 as SCSI1 using its persistent disk ID. Debian manages the disk with a GPT partition and ext4 filesystem labeled `media`, mounted read/write at `/srv/media` by UUID, with approximately 5.4 TiB available. The media directory tree is in place with `ian:ian` ownership and setgid permissions. Docker Engine and Compose are installed, and both the `hello-world` run and an Alpine container write/delete test against the Seagate bind mount passed. The media-stack Compose configuration is version-controlled in GitHub, downloaded to the Debian VM, and passes `docker compose config --quiet` validation. Jellyfin, Radarr, Sonarr, Prowlarr, and qBittorrent images were pulled and all five containers started successfully. Jellyfin reports healthy; Radarr, Sonarr, Prowlarr, and qBittorrent report running. Initial setup is complete in all five applications. Radarr and Sonarr use the shared Seagate paths and qBittorrent download client; Prowlarr is configured to connect to both applications. End-to-end import, hardlink, and playback validation remains. The Docker Compose definition for Jellyfin, Radarr, Sonarr, Prowlarr, and qBittorrent is maintained in [services/media-stack/compose.yaml](services/media-stack/compose.yaml). The Alpine container test ran as UID/GID 1000:1000, wrote and deleted a file through the Seagate bind mount, and passed. Nginx Proxy Manager is now deployed in a separate Docker Compose project on the Debian VM. A Let's Encrypt certificate has been issued for `moxxboxx.com`, and HTTPS access through NPM to Jellyfin at `jellyfin:8096` has been verified. NPM currently reaches Jellyfin through a manually attached connection to `media-stack_default`; the external-network declaration still needs to be added to NPM's Compose file so the connection survives container recreation. Dynamic DNS and Pi-hole are planned additions. qBittorrent VPN isolation with a fail-closed kill switch is also planned; see [Media Stack Notes](services/media-stack/README.md). See [Project Status](docs/project-status.md), [Network Architecture](docs/network-architecture.md), [Reverse Proxy Notes](services/reverse-proxy/README.md), [DNS Services Plan](services/dns/README.md), [Installation Notes](virtualization/proxmox-installation.md), [Host Baseline](virtualization/proxmox-host-baseline.md), [Hardware Inventory](docs/hardware-inventory.md), and the [Media Storage Design](storage/storage-layout.md).

## Project goals

- Administer a Proxmox VE virtualization host.
- Deploy and maintain Debian virtual machines and Linux containers.
- Host and maintain self-managed applications, including Jellyfin and supporting media services.
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

Jellyfin is reachable at [https://moxxboxx.com](https://moxxboxx.com) through Nginx Proxy Manager (NPM), with a Let's Encrypt certificate. NPM listens on TCP ports 80 and 443 for web traffic; its administration interface uses port 81 and is intended to remain LAN-only. NPM forwards the domain to Jellyfin over the Docker network using the `jellyfin:8096` service address. The NPM container was manually connected to the media stack's Docker network after an initial 502 Bad Gateway; this runtime connection works, but still needs to be declared in the NPM Compose file as an external network for persistence.

Dynamic DNS is planned so the domain's DNS record can follow the home's public IP address if it changes. Pi-hole is planned as a LAN-only DNS service for network-level filtering and local DNS records. The DNS provider and update method, and whether the router or Pi-hole will provide DHCP, remain to be selected.

qBittorrent VPN isolation is also planned. The proposed design uses Gluetun as the VPN gateway and places qBittorrent in Gluetun's network namespace so the VPN firewall blocks traffic when the tunnel is unavailable. ExpressVPN can be used with Gluetun through OpenVPN; a provider with supported VPN port forwarding may be evaluated later if inbound peer connectivity becomes a priority.

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
   - [x] Configure time and administrative settings.
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
   - [ ] Configure a filesystem, mount point, and storage layout.
   - [ ] Deploy Jellyfin.
4. **Application hosting and monitoring**
   - [ ] Deploy Docker workloads in a Debian VM.
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
- [Media Stack Compose](services/media-stack/compose.yaml) — Jellyfin, Radarr, Sonarr, Prowlarr, and qBittorrent service definition.
- [Media Stack Notes](services/media-stack/README.md) — service ports, storage mappings, deployment notes, and planned VPN isolation.
- [Reverse Proxy Notes](services/reverse-proxy/README.md) — Nginx Proxy Manager, the `moxxboxx.com` Jellyfin endpoint, and the persistent Docker network plan.
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
