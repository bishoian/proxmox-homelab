# Project Status

**Owner:** Ian Bishop  
**Last updated:** 2026-11-16

Proxmox VE and the Debian media-services VM are running. Radarr and Sonarr correctly import and sort media with the expected metadata. Jellyfin's automatic library refresh and playback are verified, and Seerr's external HTTPS request portal is working. The Gluetun/qBittorrent VPN path passed a fail-closed test and recovered after a full VM reboot.

Mealie and Audiobookshelf are defined in the Compose project but not deployed. Remaining work includes hardlink verification, mount persistence verification, backups, monitoring, Dynamic DNS, Pi-hole, and network documentation.

This checklist reflects verified results. Historical observations are retained in the evidence log where they help explain how the build progressed.

## 1. Proxmox host

- [x] Install Proxmox VE on the Lenovo's internal NVMe.
- [x] Enable VT-x and VT-d in BIOS.
- [x] Verify the `pve` node and web interface.
- [x] Confirm CPU, memory, NVMe layout, and 1 Gbps Ethernet link.
- [x] Configure Proxmox VE and Ceph no-subscription repositories.
- [x] Refresh package lists and apply available upgrades.
- [x] Reboot and verify kernel `7.0.14-29-pve` and `pve-manager 9.2.27`.
- [ ] Record BIOS/UEFI version and RAM module layout.
- [ ] Document host recovery and console access.

## 2. Storage

- [x] Connect the TerraMaster D4-320 and detect the installed Seagate Exos disk.
- [x] Review initial SMART attributes and record the overall `PASSED` status.
- [x] Record the extended SMART test as aborted at 80% remaining. This is incomplete, not a pass.
- [x] Identify the disk by a persistent host-side `/dev/disk/by-id` path.
- [x] Pass the disk through to Debian VM 100 as SCSI1.
- [x] Inspect the disk with `lsblk -f` and `wipefs --no-act` before formatting.
- [x] Create a GPT partition and ext4 filesystem labeled `media`.
- [x] Configure a UUID-based `/etc/fstab` mount at `/srv/media`.
- [x] Verify the mount with `mount -a`, `findmnt`, and `df -hT`.
- [x] Create the media/download directory structure and configure UID/GID 1000 permissions.
- [x] Verify non-root container write access with an Alpine test container.
- [ ] Recheck the mounted device source after a Debian VM reboot.
- [ ] Complete an extended SMART self-test during a maintenance window.
- [ ] Establish and test independent backups.

## 3. Debian VM and Docker

- [x] Create VM 100 with four vCPUs, 4 GiB RAM, a 64 GiB NVMe-backed system disk, VirtIO networking, and QEMU Guest Agent.
- [x] Install Debian 13.7 and verify first boot.
- [x] Verify SSH, DNS, package repository access, and sudo.
- [x] Verify remote SSH login from Windows PowerShell.
- [x] Verify QEMU Guest Agent communication from Proxmox.
- [x] Install Docker Engine and Compose.
- [x] Validate Docker with the official `hello-world` container.
- [x] Create and validate the version-controlled media-stack Compose project.
- [ ] Establish a routine for Debian updates and maintenance.

## 4. Media applications

- [x] Deploy Jellyfin, Radarr, Sonarr, Prowlarr, qBittorrent, and Gluetun.
- [x] Configure Radarr and Sonarr library roots and qBittorrent download categories.
- [x] Connect Prowlarr to Radarr and Sonarr; both application tests passed.
- [x] Verify Radarr and Sonarr import and sort media with the expected metadata.
- [x] Deploy and configure Seerr with Jellyfin, Radarr, and Sonarr.
- [x] Verify Seerr's external HTTPS request portal.
- [x] Enable Jellyfin real-time monitoring for Movies and Shows.
- [x] Verify automatic Jellyfin library refresh without a manual scan.
- [x] Verify Jellyfin playback using a newly downloaded movie.
- [ ] Verify hardlink/inode behavior between download and library paths.
- [ ] Deploy Mealie and Audiobookshelf and verify persistent storage.
- [ ] Document service health checks, updates, and recovery.
- [ ] Configure and test backups for application configuration and metadata.

## 5. Reverse proxy and DNS

- [x] Deploy Nginx Proxy Manager in a separate Compose project.
- [x] Configure and verify Jellyfin HTTPS access.
- [x] Configure and verify Seerr's external HTTPS request endpoint.
- [x] Connect NPM to `media-stack_default` and make the attachment persistent in its Compose project.
- [x] Verify NPM remains attached to both Docker networks after container recreation.
- [x] Correct NPM container DNS resolution for the Let's Encrypt API.
- [ ] Verify NPM's network attachment after a full Debian VM reboot.
- [ ] Select and test a Dynamic DNS provider/updater.
- [ ] Deploy Pi-hole for LAN DNS filtering and local records.
- [ ] Decide whether DHCP remains on the router or moves to Pi-hole.

Public hostnames are masked throughout this repository.

## 6. VPN isolation

- [x] Configure Gluetun with ExpressVPN OpenVPN.
- [x] Route qBittorrent through Gluetun's network namespace.
- [x] Bind qBittorrent to `tun0`.
- [x] Verify Radarr and Sonarr reach qBittorrent through `gluetun:8080`.
- [x] Verify qBittorrent's public IP matches Gluetun's VPN egress.
- [x] Bring `tun0` down and verify a direct-IP HTTPS request is blocked.
- [x] Recreate the services and verify VPN recovery.
- [x] Reboot the Debian VM and verify Gluetun, qBittorrent, and application connectivity recover.
- [ ] Repeat the outage test after future Gluetun or qBittorrent image updates.

## 7. Networking and future lab work

- [ ] Identify the RealHD switch's exact model, hardware revision, and firmware.
- [ ] Trace rack cabling and document switch port assignments.
- [ ] Verify router capabilities relevant to VLANs.
- [ ] Design and implement VLANs with a tested recovery path.
- [ ] Create Windows Server and Windows client VMs.
- [ ] Build an isolated Active Directory practice domain.
- [ ] Practice DNS, DHCP, users, groups, and Group Policy.

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
| 2026-11-16 | Mealie and Audiobookshelf | Added service definitions to Compose; deployment remains pending |
| 2026-11-15 | Proxmox installation and update | Installed Proxmox VE 9.2.2, applied updates, rebooted, and verified kernel `7.0.14-29-pve` |
| 2026-11-15 | Debian VM commissioning | Installed Debian 13.7; verified SSH, DNS, APT, sudo, and QEMU Guest Agent |
| 2026-11-15 | Seagate storage commissioning | Passed through the disk, created GPT/ext4, mounted at `/srv/media`, and validated container write permissions |
| 2026-11-15 | Initial media stack | Deployed the core containers and completed initial application setup |
| 2026-11-15 | D4-320 diagnosis | Found the enclosure's rear power switch was off; powering it on restored disk detection |

The full implementation details live in the relevant service, virtualization, storage, and troubleshooting documents.
