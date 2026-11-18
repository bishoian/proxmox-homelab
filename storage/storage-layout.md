# Media Storage Design

**Host:** Lenovo ThinkCentre M920q Tiny  
**Enclosure:** TerraMaster D4-320 USB DAS  
**Data disks:** 2 × Seagate Exos ST6000NM0115, 6 TB each  
**Consumer:** Debian media-services VM 100  
**Status:** Media and shared-storage filesystems are configured; reboot mount-source verification and independent backup planning remain open.

## Design

I dedicated the first 6 TB disk to media and downloads for my Debian VM and installed the second matching disk as independent shared storage. Each disk has its own standalone ext4 filesystem. I have not configured RAID, MergerFS, or a pooled volume.

I pass the physical disk through to VM 100 as SCSI1 using a persistent `/dev/disk/by-id` identifier. I omit the exact identifier and serial from this public repository. Debian manages the partition table, filesystem, mount, and directories.

## Storage placement

| Device | Location | Purpose |
|---|---|---|
| Lenovo 256 GB NVMe | Proxmox host | Hypervisor and Debian VM system disk |
| First Seagate Exos 6 TB | D4-320, passed through to Debian as SCSI1 | Media library and downloads at `/srv/media` |
| Second Seagate Exos 6 TB | D4-320, passed through to Debian as SCSI2 | Shared files at `/srv/shared` |

## Filesystem and directory layout

I created one GPT partition (`/dev/sdb1`), formatted it as ext4, and labeled it `media`. I added a UUID-based `/etc/fstab` entry to mount it at `/srv/media`. I used `mount -a`, `findmnt`, and `df -hT` to confirm a read/write mount with approximately 5.4 TiB available. I still need to recheck the mount source after a VM reboot.

```text
/srv/media/data/
├── torrents/
│   ├── movies/
│   └── tv/
├── usenet/
│   ├── incomplete/
│   └── complete/
│       ├── movies/
│       └── tv/
└── media/
    ├── movies/
    ├── tv/
    └── music/

/srv/media/data/
├── audiobooks/
└── podcasts/
```

I set the directory tree ownership to UID/GID 1000 (`ian:ian`) and directory permissions to `2775` with setgid inheritance. I verified non-root access by running an Alpine container as UID/GID 1000:1000 and creating and deleting a test file through the bind mount.

## Shared storage

I passed the second disk through to VM 100 as a separate SCSI device. In Debian, I created one GPT partition, formatted it as ext4, and labeled it `shared`. I configured a UUID-based `/etc/fstab` entry for `/srv/shared` and verified the mount with `mount -a`, `findmnt`, and `df -hT`. I have not yet verified the mount after a full VM reboot.

I created this directory structure:

```text
/srv/shared/
├── Games/
├── ISOs/
├── Backups/
├── Documents/
├── Installers/
└── General/
```

I created the `shared` Linux group (GID 1500), added my account and a second account to it, and configured the shared directories with group ownership and setgid permissions. I deployed Samba in Docker to publish the `Shared` SMB share over TCP 445 on the LAN interface. I verified local SMB read/write access for both accounts and connected my Windows PC. The second household PC has not been tested yet.

## Container path strategy

I mapped the same host data tree to `/data` in Radarr, Sonarr, and qBittorrent, and mounted the media library read-only at `/media` in Jellyfin. I chose this shared-path layout because keeping downloads and library folders on one filesystem allows hardlinks and atomic moves when paths and permissions are configured consistently.

I created `/srv/media/data/audiobooks` and `/srv/media/data/podcasts` for Audiobookshelf and assigned them to UID/GID 1000. I have not yet directly verified hardlink/inode behavior. I have separately verified application sorting, metadata, imports, Jellyfin library refresh, and playback.

Application configuration and databases remain on the VM's NVMe-backed system disk. The media disk is separate from the VM's virtual system disk, so a VM backup alone will not protect its contents.

## Drive health

The initial SMART overall status was `PASSED`, and the captured attributes showed no reallocated, pending, offline-uncorrectable, reported-uncorrectable, or UDMA CRC errors. The extended SMART test was aborted by the host at 80% remaining. It is incomplete, not a pass. See [Seagate SMART Baseline](seagate-smart-baseline.md).

## Implementation status

- [x] Select a standalone ext4 data disk for the Debian media VM.
- [x] Identify the disk with a persistent host-side `/dev/disk/by-id` path.
- [x] Attach the disk to VM 100 as SCSI1.
- [x] Confirm Debian detects the separate 5.5 TiB disk.
- [x] Inspect signatures with `lsblk -f` and `wipefs --no-act` before formatting.
- [x] Create a GPT partition and ext4 filesystem labeled `media`.
- [x] Configure and test the UUID-based `/etc/fstab` mount at `/srv/media`.
- [x] Create the shared directory structure and configure UID/GID 1000 permissions.
- [x] Verify non-root container write access.
- [x] Install the second 6 TB disk and pass it through to VM 100 as SCSI2.
- [x] Create a GPT/ext4 filesystem labeled `shared` and configure its UUID-based mount at `/srv/shared`.
- [x] Create shared directories and configure group-based permissions for Samba access.
- [x] Deploy Samba and verify authenticated local SMB access for both accounts and Windows access from my PC.
- [ ] I will reboot my Debian VM and confirm that both expected disks remain mounted at `/srv/media` and `/srv/shared`.
- [ ] I will verify Samba starts and the Windows share reconnects after the VM reboot.
- [ ] I will compare inode numbers between a downloaded file and its library copy to verify hardlink behavior.
- [x] I created `/srv/media/data/audiobooks` and `/srv/media/data/podcasts` and assigned them to UID/GID 1000 before starting Audiobookshelf.
- [ ] I will define and test independent backups for my VM configuration and important media data.
