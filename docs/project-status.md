# Project Status

**Owner:** Ian Bishop  
**Last updated:** 2026-11-17

I have Proxmox VE and my Debian media-services VM running. I verified that Radarr and Sonarr import and sort media with the expected metadata, that Jellyfin automatically refreshes its library and plays new media, and that Seerr's external HTTPS request portal works. I also tested the Gluetun/qBittorrent VPN path for fail-closed behavior and confirmed that it recovered after a full VM reboot. I installed the second 6 TB disk as independent shared storage, deployed Samba, and connected my Windows PC to the authenticated SMB share.

I deployed Mealie and Audiobookshelf from the media-stack Compose project. Mealie reports healthy, and both services returned HTTP 200 over `media-stack_default`. I configured their NPM proxy hosts, updated and verified their HTTPS certificates, and confirmed that both HTTPS routes work. I also completed the administrator and first-run setup steps for both applications. I still need to verify recipe import, audiobook and podcast library scanning, playback, and data persistence after container recreation. My remaining work includes verifying both storage mounts after a VM reboot, testing the shared folder from my girlfriend's PC after she moves in, verifying hardlinks, establishing backups and monitoring, selecting Dynamic DNS, deploying Pi-hole, and documenting my network.

I use this checklist to separate work I have verified from work I still intend to complete. The evidence log records key milestones and the results I observed.

## 1. Proxmox host

- [x] I installed Proxmox VE on the Lenovo's internal NVMe.
- [x] I enabled VT-x and VT-d in BIOS.
- [x] I verified the `pve` node and web interface.
- [x] I confirmed the CPU, memory, NVMe layout, and 1 Gbps Ethernet link.
- [x] I configured the Proxmox VE and Ceph no-subscription repositories.
- [x] I refreshed package lists and applied available upgrades.
- [x] I rebooted and verified kernel `7.0.14-29-pve` and `pve-manager 9.2.27`.
- [ ] I will record the BIOS/UEFI version and RAM module layout.
- [ ] I will document my host recovery and console-access process.

## 2. Storage

- [x] I connected the TerraMaster D4-320 and confirmed that Proxmox detected both installed Seagate Exos disks.
- [x] I reviewed the initial SMART attributes and recorded the overall `PASSED` status.
- [x] I recorded the extended SMART test as aborted at 80% remaining. This is incomplete, not a pass.
- [x] I identified the disk by a persistent host-side `/dev/disk/by-id` path.
- [x] I passed the disk through to Debian VM 100 as SCSI1.
- [x] I inspected the disk with `lsblk -f` and `wipefs --no-act` before formatting.
- [x] I created a GPT partition and ext4 filesystem labeled `media`.
- [x] I configured a UUID-based `/etc/fstab` mount at `/srv/media`.
- [x] I verified the mount with `mount -a`, `findmnt`, and `df -hT`.
- [x] I created the media/download directory structure and configured UID/GID 1000 permissions.
- [x] I verified non-root container write access with an Alpine test container.
- [ ] I will reboot the Debian VM and confirm that the expected device remains mounted at `/srv/media`.
- [ ] I will run and record a complete extended SMART self-test during a maintenance window.
- [ ] I will establish independent backups and perform a restore test.

### Shared storage and Samba

- [x] I installed the second 6 TB Seagate disk in the D4-320 and passed it through to VM 100 as SCSI2.
- [x] I checked its initial SMART report. The overall assessment passed, the captured error-related attributes were zero, and the drive reported 28°C. No self-test has been recorded yet.
- [x] I created a GPT partition and ext4 filesystem labeled `shared`.
- [x] I configured a UUID-based `/etc/fstab` mount at `/srv/shared` and verified it with `mount -a`, `findmnt`, and `df -hT`.
- [x] I created folders for Games, ISOs, Backups, Documents, Installers, and General.
- [x] I created the `shared` Linux group (GID 1500), configured setgid directory permissions, and added both Samba identities to the group.
- [x] I deployed Samba in the media-stack Compose project. The container reached healthy status and publishes TCP 445 on the Debian VM's LAN interface only.
- [x] I verified both accounts in Samba's account database and completed local SMB authentication and directory write tests for both.
- [x] I connected my Windows PC to the share and mapped it as a persistent network drive.
- [ ] I will verify that the shared filesystem and Samba service recover after a full Debian VM reboot.
- [ ] I will test access from my girlfriend's PC after she moves in.

## 3. Debian VM and Docker

- [x] I created VM 100 with four vCPUs, 4 GiB RAM, a 64 GiB NVMe-backed system disk, VirtIO networking, and QEMU Guest Agent.
- [x] I installed Debian 13.7 and verified its first boot.
- [x] I verified SSH, DNS, package repository access, and sudo.
- [x] I verified remote SSH login from Windows PowerShell.
- [x] I verified QEMU Guest Agent communication from Proxmox.
- [x] I installed Docker Engine and Compose.
- [x] I validated Docker by running the official `hello-world` container.
- [x] I created and validated the version-controlled media-stack Compose project.
- [ ] I will define a routine for Debian updates and maintenance.

## 4. Media applications

- [x] I deployed Jellyfin, Radarr, Sonarr, Prowlarr, qBittorrent, and Gluetun.
- [x] I configured Radarr and Sonarr library roots and qBittorrent download categories.
- [x] I connected Prowlarr to Radarr and Sonarr and confirmed that both application tests passed.
- [x] I verified that Radarr and Sonarr import and sort media with the expected metadata.
- [x] I deployed Seerr and connected it to Jellyfin, Radarr, and Sonarr.
- [x] I verified Seerr's external HTTPS request portal.
- [x] I enabled Jellyfin real-time monitoring for Movies and Shows.
- [x] I verified automatic Jellyfin library refresh without a manual scan.
- [x] I verified Jellyfin playback using a newly downloaded movie.
- [ ] I will compare inode numbers between a downloaded file and its library copy to verify whether hardlinks are being used.
- [x] I deployed Mealie and Audiobookshelf from the media-stack Compose project. Mealie reports healthy, and both services returned HTTP 200 over `media-stack_default`.
- [x] I completed administrator and first-run setup for Mealie and Audiobookshelf.
- [ ] I will verify recipe import, audiobook and podcast library scanning, playback, and data persistence after container recreation.
- [ ] I will document how I check service health, apply updates, and recover services.
- [ ] I will configure backups for application configuration and metadata, then test a restore.

## 5. Reverse proxy and DNS

- [x] I deployed Nginx Proxy Manager in a separate Compose project.
- [x] I configured and verified Jellyfin HTTPS access.
- [x] I configured and verified Seerr's external HTTPS request endpoint.
- [x] I connected NPM to `media-stack_default` and made the attachment persistent in its Compose project.
- [x] I verified that NPM remains attached to both Docker networks after container recreation.
- [x] I corrected NPM container DNS resolution for the Let's Encrypt API.
- [x] I configured NPM proxy hosts and updated/verified HTTPS certificates for Mealie and Audiobookshelf.
- [x] I verified that both household applications work through their HTTPS routes.
- [ ] I will reboot the Debian VM and confirm that NPM reconnects to both Docker networks.
- [ ] I will choose a Dynamic DNS provider and test that its updater follows changes to my WAN address.
- [ ] I plan to deploy Pi-hole for LAN DNS filtering and local records.
- [ ] I will decide whether to keep DHCP on my router or move it to Pi-hole before enabling Pi-hole's DHCP service.

Public hostnames are masked throughout this repository.

## 6. VPN isolation

- [x] I configured Gluetun with ExpressVPN OpenVPN.
- [x] I routed qBittorrent through Gluetun's network namespace.
- [x] I bound qBittorrent to `tun0`.
- [x] I verified that Radarr and Sonarr reach qBittorrent through `gluetun:8080`.
- [x] I verified that qBittorrent's public IP matches Gluetun's VPN egress.
- [x] I brought `tun0` down and verified that a direct-IP HTTPS request was blocked.
- [x] I recreated the services and verified VPN recovery.
- [x] I rebooted the Debian VM and verified that Gluetun, qBittorrent, and application connectivity recovered.
- [ ] I will repeat the VPN outage test after future Gluetun or qBittorrent image updates.

## 7. Networking and future lab work

- [ ] I will identify my RealHD switch's exact model, hardware revision, and firmware.
- [ ] I will trace my rack cabling and document switch port assignments.
- [ ] I will verify that my router supports the VLAN interfaces, routing, DHCP, and firewall policies I intend to use.
- [ ] I plan to design VLANs for management, servers, trusted devices, and lab/testing. Before moving devices, I will validate router support and test a recovery path so I do not lose management access.
- [ ] I plan to create Windows Server and Windows client VMs.
- [ ] I plan to build an isolated Active Directory practice domain.
- [ ] I plan to use the domain to practice DNS, DHCP, user and group management, and Group Policy.

## Troubleshooting records

- [D4-320 drive not detected](../troubleshooting/d4-320-drive-not-detected.md): found the enclosure's rear power switch was off.
- [Docker DNS and reverse proxy](../troubleshooting/docker-dns-and-reverse-proxy.md): corrected Docker DNS forwarding failures and NPM's initial 502 caused by separate networks.

## Evidence log

| Date | Activity | Result |
|---|---|---|
| 2026-11-16 | Media application verification | Radarr and Sonarr correctly imported and sorted media with expected metadata; Jellyfin automatically refreshed and played a newly downloaded movie |
| 2026-11-16 | Seerr external verification | Request portal, reverse-proxy route, and HTTPS endpoint confirmed working |
| 2026-11-16 | Docker DNS corrections | Explicit resolvers restored lookups for Radarr, Sonarr, Prowlarr, and NPM |
| 2026-11-16 | VPN kill-switch and reboot test | Direct-IP egress was blocked with `tun0` down; Gluetun and qBittorrent recovered after recreation and VM reboot |
| 2026-11-17 | Shared storage and Samba | Installed and passed through second disk; created ext4 `/srv/shared`; deployed authenticated LAN-only SMB share; verified both local SMB accounts and Windows access from my PC |
| 2026-11-17 | Mealie and Audiobookshelf | Deployed both services; completed administrator and first-run setup; configured NPM proxy hosts and verified HTTPS certificates and routes; functional library/import/playback and persistence tests remain |
| 2026-11-15 | Proxmox installation and update | Installed Proxmox VE 9.2.2, applied updates, rebooted, and verified kernel `7.0.14-29-pve` |
| 2026-11-15 | Debian VM commissioning | Installed Debian 13.7; verified SSH, DNS, APT, sudo, and QEMU Guest Agent |
| 2026-11-15 | Seagate storage commissioning | Passed through the disk, created GPT/ext4, mounted at `/srv/media`, and validated container write permissions |
| 2026-11-15 | Initial media stack | Deployed the core containers and completed initial application setup |
| 2026-11-15 | D4-320 diagnosis | Found the enclosure's rear power switch was off; powering it on restored disk detection |

I keep implementation details in the relevant service, virtualization, storage, and troubleshooting documents.
