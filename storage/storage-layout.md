# Media Storage Design

**Host:** Lenovo ThinkCentre M920q Tiny  
**Hypervisor:** Proxmox VE  
**Enclosure:** TerraMaster D4-320  
**Initial data disk:** 1 × Seagate Exos ST6000NM0115, 6 TB  
**Workload:** Debian media-services VM (Jellyfin and supporting containers)  
**Status:** Design selected; disk passthrough and filesystem setup not yet implemented.

## Design decision

The first 6 TB Seagate will be used as a dedicated data disk for a Debian virtual machine running the media stack. The second matching 6 TB drive will remain unused until a later expansion phase.

The two drives will not be combined into RAID, MergerFS, or another pool as part of the initial build.

## Storage placement

| Device | Location | Purpose |
|---|---|---|
| Lenovo 256 GB NVMe | Proxmox host | Hypervisor and Debian VM system disk |
| First Seagate Exos 6 TB | D4-320, passed through to Debian VM | Media library, downloads, and application data |
| Second Seagate Exos 6 TB | D4-320, reserved | Future capacity expansion |

The Debian VM's virtual system disk and container configuration will remain on the NVMe-backed Proxmox storage. The 6 TB disk will be attached to the VM as a physical disk using its persistent `/dev/disk/by-id` path. Debian will own the partition table, filesystem, mount, and directory structure on that disk.

The physical disk's identity must be checked immediately before passthrough. The current `/dev/sda` name is not suitable for a persistent VM configuration because Linux device enumeration can change.

## Filesystem and directory structure

The selected filesystem is ext4, created inside the Debian VM after the disk has been passed through and positively identified.

Proposed mount point:

`/srv/media`

Proposed data structure:

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

The download and library folders will live on the same ext4 filesystem. This allows Radarr and Sonarr to use hardlinks and atomic moves when the download client and applications have consistent paths and permissions. Hardlinks cannot span separate filesystems or mounts.

## Container path strategy

The media stack will run in Docker Compose inside Debian. A single host data directory will be mounted into the relevant containers at the same container path:

- Radarr and Sonarr: `/data`
- Download client: `/data`
- Jellyfin: read-only access to `/data/media`
- Prowlarr: application configuration and network access; no direct media-file access required

Application configuration and databases will live on the Debian VM's system disk, separate from the large media data tree.

The initial application stack is expected to include:

- Jellyfin for media playback.
- Radarr for movie library management.
- Sonarr for television library management.
- Prowlarr for indexer management.
- A download client selected during deployment.
- Jellyseerr for media requests, if desired.
- Bazarr for subtitle management, if desired.

Folder ownership and shared group permissions will be configured consistently across the containers. The path strategy follows the guidance in [TRaSH Guides: Docker](https://trash-guides.info/Hardlinks/How-to-setup-for/Docker/) and the [Servarr Docker Guide](https://wiki.servarr.com/docker-guide).

## Disk passthrough and backup implications

The Seagate will be passed through to the Debian VM as a physical disk. Proxmox will manage the VM's virtual system disk, while Debian will manage the Seagate's ext4 filesystem.

The media disk will not be part of the Debian VM's ordinary virtual system disk. VM backups therefore need to be paired with a separate backup plan for important media and application data. The second drive, when commissioned, may become a local backup target or additional storage, but a second disk in the same enclosure is not an independent off-site backup.

## SMART and drive condition

The initial SMART overall result was `PASSED`. The captured attribute table showed zero reallocated sectors, current pending sectors, offline-uncorrectable sectors, reported uncorrectable errors, and UDMA CRC errors, with a reported temperature of 24°C.

The extended SMART self-test was started but will be stopped before completion to proceed with the build. Its result must be recorded as aborted/incomplete, not passed. The initial SMART snapshot is encouraging but does not replace a completed extended read test. The disk will be used for media data with the understanding that important files need a separate backup.

## Implementation checklist

- [ ] Stop the current extended SMART self-test and confirm the recorded state.
- [ ] Reboot Proxmox to activate the installed kernel updates.
- [ ] Create a Debian VM with its system disk on the NVMe.
- [ ] Identify the Seagate using a persistent `/dev/disk/by-id` path.
- [ ] Attach the physical Seagate disk to the Debian VM.
- [ ] Confirm the disk appears in Debian and identify it by model and capacity.
- [ ] Create a GPT partition table and ext4 filesystem inside Debian.
- [ ] Mount the filesystem persistently by UUID at `/srv/media`.
- [ ] Create the shared `/srv/media/data` directory structure.
- [ ] Configure Docker Compose and consistent container paths/permissions.
- [ ] Deploy Jellyfin, Radarr, Sonarr, Prowlarr, and a download client.
- [ ] Test imports, hardlinks, playback, and permissions.
- [ ] Define and test backup coverage for VM configuration and important media data.
