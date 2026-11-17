# Media Storage Design

**Host:** Lenovo ThinkCentre M920q Tiny  
**Enclosure:** TerraMaster D4-320 USB DAS  
**Data disk:** 1 × Seagate Exos ST6000NM0115, 6 TB  
**Consumer:** Debian media-services VM 100  
**Status:** Configured and in use; reboot mount-source verification and independent backup planning remain open.

## Design

The first 6 TB disk is dedicated to media and downloads for the Debian VM. The second matching disk is reserved for later expansion. The initial design uses a standalone ext4 filesystem, not RAID, MergerFS, or a pooled volume.

The physical disk is passed through to VM 100 as SCSI1 using a persistent `/dev/disk/by-id` identifier. The exact identifier and serial are intentionally omitted. Debian manages the partition table, filesystem, mount, and directories.

## Storage placement

| Device | Location | Purpose |
|---|---|---|
| Lenovo 256 GB NVMe | Proxmox host | Hypervisor and Debian VM system disk |
| First Seagate Exos 6 TB | D4-320, passed through to Debian | Media library and downloads |
| Second Seagate Exos 6 TB | Reserved | Future expansion |

## Filesystem and directory layout

The Seagate has one GPT partition, `/dev/sdb1`, formatted as ext4 and labeled `media`. A UUID-based `/etc/fstab` entry mounts it at `/srv/media`. `mount -a`, `findmnt`, and `df -hT` confirmed a read/write mount with approximately 5.4 TiB available. The mount source has not yet been rechecked after a VM reboot.

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
```

The directory tree is owned by UID/GID 1000 (`ian:ian`). Directories use mode `2775` with setgid inheritance. A non-root Alpine container running as UID/GID 1000:1000 successfully created and deleted a test file in the bind mount.

## Container path strategy

Radarr, Sonarr, and qBittorrent share the same host data tree at the same container path, `/data`. Jellyfin receives the media library read-only at `/media`. Keeping downloads and library folders on one filesystem allows hardlinks and atomic moves when paths and permissions are configured consistently.

Hardlink/inode behavior has not yet been directly verified. Application sorting, metadata, imports, Jellyfin refresh, and playback have been verified separately.

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
- [ ] Recheck the mounted source after a Debian VM reboot.
- [ ] Verify hardlink behavior between download and library paths.
- [ ] Define and test independent backups for VM configuration and important media data.
