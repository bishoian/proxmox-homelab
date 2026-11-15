# Proposed Storage Layout

**Host:** Lenovo ThinkCentre M920q Tiny  
**Hypervisor:** Proxmox VE 9.2.2  
**External enclosure:** TerraMaster D4-320  
**Drives:** 2 × Seagate Exos ST6000NM0115, 6 TB each  
**Priority:** Maximum usable capacity for general homelab storage  
**Status:** Proposed design; not yet implemented.

## Design goals

- Use the capacity of both 6 TB drives.
- Keep the two physical disks independently readable.
- Avoid making one disk failure take down the entire storage set.
- Keep Proxmox and active VM operating systems on the internal NVMe.
- Provide bulk storage for media, application data, downloads, and general files.
- Support local Proxmox backups while recognizing that a second disk in the same enclosure is not an off-site backup.

## Recommended layout

| Device | Proposed filesystem | Proposed mount point | Intended use |
|---|---|---|---|
| Internal NVMe | Existing EFI + LVM-thin layout | Existing Proxmox paths | Proxmox host and active VM/CT system disks |
| Seagate Exos 1 | ext4 | `/mnt/bulk-a` | Media, downloads, shared files, general datasets |
| Seagate Exos 2 | ext4 | `/mnt/bulk-b` | Additional bulk files, application data, and local backup target |

Each HDD will have its own filesystem and UUID-based mount. Device names such as `/dev/sda` can change across boots or USB reconnection, so permanent mounts should use filesystem UUIDs rather than relying on the current device path.

## Optional combined namespace

After both disks are individually mounted and tested, MergerFS can combine the two directories into a single path such as `/mnt/storage`.

MergerFS is a union filesystem, not RAID:

- Files are stored whole on one underlying disk rather than striped across both.
- The combined path presents the free space of both filesystems.
- A failure of one disk affects the files stored on that disk; files on the other disk remain on that disk.
- MergerFS does not provide mirroring, parity, or backup protection.

The two independent mount points should remain available for diagnostics and recovery even if a combined path is added.

MergerFS is intended for bulk file storage such as media and shared folders. It will not be used as the backing store for active VM boot disks or latency-sensitive workloads.

## Why not RAID 0?

RAID 0 can combine both drive capacities, but it stripes data across the drives. A failure of either drive can make the entire striped array unavailable. The same nominal capacity can be obtained with two independent filesystems, so RAID 0 offers no compelling capacity advantage for this build.

The D4-320 itself does not provide hardware RAID and exposes individual disks. It supports software-managed layouts through the host, but no RAID array is planned.

## Proxmox integration

The internal NVMe's existing `local-lvm` thin pool will remain the default location for active VM and container system disks.

After the HDD filesystems are created and mounted persistently, each mount can be added to Proxmox as a Directory storage. Proxmox Directory storage can hold content such as backups, ISO images, templates, snippets, and file-based VM images. For this build, the initial HDD directory definitions should be limited to the content types actually needed; active VM system disks should remain on the NVMe.

Bulk media and application data can be exposed to a Debian service VM through a deliberately configured share or mount. The exact access method will be selected when the first service VM is designed.

## Backup strategy

The second HDD can hold local backups of VM and container configurations stored on the NVMe, as well as selected application data. This protects against some individual-drive failures when backups are kept separately, but both drives remain in the same USB enclosure and connected to the same host.

Important data should eventually have another backup copy outside this enclosure, such as a separate external disk or another system.

## Capacity expectations

Two 6 TB drives provide 12 TB of nominal raw capacity. Linux typically displays approximately 5.46 TiB per disk, or about 10.9 TiB combined before filesystem overhead.

## Implementation checklist

- [ ] Finish the extended SMART self-test on the first Seagate.
- [ ] Install the second Seagate when it arrives.
- [ ] Verify its model, capacity, SMART status, and extended self-test.
- [ ] Confirm stable USB detection of both drives.
- [ ] Create an ext4 filesystem on each disk after confirming the correct device identities.
- [ ] Record each filesystem UUID.
- [ ] Create persistent mounts using UUIDs.
- [ ] Add the two mounted filesystems as Proxmox Directory storage with appropriate content types.
- [ ] Test read/write access and persistence after a controlled reboot.
- [ ] Consider MergerFS for a unified bulk-storage path.
- [ ] Configure and test backup jobs.
