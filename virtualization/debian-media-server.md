# Debian Media Server VM

**VM:** 100 (`media-server`)  
**Guest OS:** Debian 13.7 (Trixie)  
**Role:** Docker host for media services and Nginx Proxy Manager

## VM configuration

| Setting | Value |
|---|---|
| vCPU | 4 cores |
| Memory | 4 GiB |
| System disk | 64 GiB on Proxmox `local-lvm` |
| Disk controller | VirtIO SCSI single |
| Firmware | SeaBIOS |
| Network | VirtIO NIC on `vmbr0` |
| QEMU Guest Agent | Installed and active |

The Debian system disk remains on the Proxmox NVMe. The Seagate data disk is passed through separately as SCSI1.

## Installation and guest validation

Debian 13.7 was installed from the amd64 netinst ISO with no desktop environment, SSH server, and standard system utilities. GRUB was installed to the VM's virtual system disk. The installer ISO was detached after installation.

Verified guest state:

| Check | Result |
|---|---|
| Hostname | `media-server` |
| Account | `ian` |
| OS | Debian GNU/Linux 13 (Trixie) |
| Architecture | x86-64 |
| Network | VirtIO interface `ens18` up with IPv4 address |
| Default route | Present through home gateway |
| DNS | `deb.debian.org` resolves |
| APT | Debian repositories reachable; package refresh succeeded |
| SSH | Active; remote login from Windows PowerShell verified |
| Sudo | `sudo -v` succeeded |
| QEMU Guest Agent | Active; Proxmox `qm agent 100 ping` completed successfully |

The QEMU Guest Agent unit is static in Debian and does not need to be enabled with `systemctl enable`. The service is active and host-to-guest communication is verified.

## Data disk passthrough

The Seagate Exos disk is attached to VM 100 as SCSI1 using a persistent host-side `/dev/disk/by-id` path. The exact path and serial are omitted from this public record.

Inside Debian, the disk appears as a separate 5.5 TiB SCSI device. Before formatting, `lsblk -f` and `wipefs --no-act` found no existing filesystem or signatures.

The disk was then:

1. Partitioned with GPT using one full-capacity partition (`/dev/sdb1`).
2. Formatted as ext4 and labeled `media`.
3. Added to `/etc/fstab` by filesystem UUID, mounted at `/srv/media`, and tested with `mount -a`.
4. Verified read/write with `findmnt` and `df -hT`.
5. Populated with the shared torrent, usenet, movie, TV, and music directory structure.

The data tree is owned by UID/GID 1000 (`ian:ian`). Directories use mode `2775` with setgid inheritance. An Alpine container running as UID/GID 1000:1000 successfully created and deleted a test file through the bind mount.

The mount source still needs to be checked after a VM reboot. Storage design and remaining tasks are in [Media Storage Design](../storage/storage-layout.md).

## Docker installation

Docker Engine and the Compose plugin were installed from Docker's official Debian Trixie repository. The Docker service reported `active`, client/server versions matched, and Compose was available. The official `hello-world` container ran successfully.

The media Compose project is stored on the VM at `/opt/media-stack`. Application configuration and Jellyfin cache are on the VM's NVMe-backed system disk; media and downloads are on the passed-through Seagate disk.

See [Media Stack](../services/media-stack/README.md) for deployed services, paths, ports, and VPN configuration.

## VPN gateway

Gluetun provides the ExpressVPN OpenVPN tunnel for qBittorrent. qBittorrent shares Gluetun's network namespace and binds to `tun0`. Credentials remain in the local root-owned `/opt/media-stack/.env` file with mode `600`; they are not stored in Git.

The kill switch was tested with downloads paused. Bringing `tun0` down caused DNS-based egress to fail, and a direct-IP HTTPS request to `1.1.1.1` returned HTTP 000 and timed out. Recreating the services restored the tunnel and qBittorrent. After a full Debian VM reboot, Gluetun returned healthy, qBittorrent retained `tun0`, VPN egress returned, and Radarr/Sonarr could again reach qBittorrent.

This verifies network-level fail-closed behavior, not automatic job-pausing behavior in qBittorrent.

## Seerr and reverse proxy

Seerr is deployed and connected to the Jellyfin Movies and Shows libraries and the Radarr/Sonarr servers. Its external HTTPS request portal has been verified.

Nginx Proxy Manager runs in a separate Compose project and publishes ports 80, 81, and 443. The NPM administration interface on port 81 is intended for LAN access only. NPM shares the media stack's Docker network so it can reach Jellyfin and Seerr by service name. Both external HTTPS routes are verified; public hostnames are masked in this repository.

See [Reverse Proxy](../services/reverse-proxy/README.md) and [Docker DNS and Reverse Proxy Troubleshooting](../troubleshooting/docker-dns-and-reverse-proxy.md).

## Remaining VM tasks

- [ ] Verify the Seagate mount source after a full Debian VM reboot.
- [ ] Establish a Debian update and maintenance routine.
- [ ] Deploy Mealie and Audiobookshelf and verify persistent storage.
- [ ] Configure backups for VM configuration, application data, and important media.
- [ ] Document routine service health checks and recovery.
