# Changelog

A concise record of the homelab's major build and documentation milestones.

## 2026-11-15

### Initial project setup
- Created the public `proxmox-homelab` repository.
- Added the project overview, hardware inventory, planned network architecture, and milestone checklist.
- Documented the Lenovo M920q, TerraMaster D4-320, Seagate Exos drives, RealHD switch, RackMate cabinet, power distribution, patch panel, shelves, and cabling.

### Proxmox installation
- Created the Proxmox VE installation USB.
- Enabled Intel VT-x and Intel VT-d in the M920q BIOS.
- Booted the installer and installed Proxmox VE 9.2.2 on the internal NVMe using ext4.
- Rebooted successfully to the Proxmox local console.
- Signed in to the web interface and confirmed the `pve` node was online.

### Storage
- Connected the TerraMaster D4-320 to the Lenovo over USB.
- Traced the initial missing-drive symptom to the enclosure's rear power switch being off.
- Powered on the enclosure and refreshed Proxmox disk inventory; the 6 TB Seagate Exos appeared as `/dev/sda`.
- Confirmed the SMART overall status reports `PASSED`.
- Reviewed the initial SMART attribute table: zero reallocated, pending, offline-uncorrectable, reported-uncorrectable, and UDMA CRC error counts; 24°C reported temperature.
- Recorded the displayed power-on-hours and head-flying-hours counters.
- Started an extended SMART self-test on the Seagate; the drive estimated approximately 10 hours to completion.

### Host baseline
- Reviewed the Proxmox node Summary: six CPUs, 15.46 GiB usable RAM, EFI boot, and Proxmox manager 9.2.2.
- Confirmed the installed processor is an Intel Core i5-8500 (standard model, not the i5-8500T).
- Identified that the Enterprise package repository is enabled but requires a subscription; repository configuration for the unsubscribed lab is pending.

### Host storage layout
- Identified the internal NVMe as a Lenovo NVMe 2280S 256GB SSD; Linux reports 238.5G.
- Recorded the Proxmox EFI and LVM layout, including the 8 GB swap volume, 69.4 GB root filesystem, and LVM-thin data pool.
- Confirmed the Seagate Exos is visible as a separate 5.5T disk with no partitions, filesystem, or mount point.

### Host hardware and network checks
- Confirmed the installed processor from the Proxmox Shell as an Intel Core i5-8500 at 3.00 GHz with six logical CPUs.
- Confirmed the physical Ethernet interface (`nic0`) is UP at 1000 Mb/s; the Proxmox bridge (`vmbr0`) uses the same uplink. The wireless interface is DOWN.
- Recorded the host's current wired link as 1 Gbps; the switch-side path and port negotiation remain to be checked.

### Host hardware and network checks
- Confirmed the installed processor from the Proxmox Shell as an Intel Core i5-8500 at 3.00 GHz with six logical CPUs.
- Confirmed the physical Ethernet interface (`nic0`) is UP at 1000 Mb/s; the Proxmox bridge (`vmbr0`) uses the same uplink. The wireless interface is DOWN.
- Recorded the host's current wired link as 1 Gbps, matching Lenovo's M920 Tiny specification for onboard Intel I219-LM Gigabit Ethernet. The switch-side path and port negotiation remain to be checked.

### Package repositories
- Disabled the Proxmox VE Enterprise and Ceph Squid Enterprise entries.
- Enabled the Proxmox VE no-subscription repository and Ceph Tentacle no-subscription repository.
- Retained the Debian Trixie and Debian Security repositories.
- Added documentation for the package source configuration.
- Refreshed the APT package database successfully; Debian, Proxmox VE no-subscription, and Ceph Tentacle repositories were reached, 530 kB was fetched, and the task ended with `TASK OK`.
- Reviewed and installed the available package upgrades without reported errors.
- Confirmed the Proxmox Updates view reports `No updates available`.
- Noted that a newer Proxmox kernel and manager package were included; reboot and active-version verification remain pending until the extended Seagate SMART test completes.

### Storage architecture planning
- Selected a capacity-focused proposal for the two 6 TB drives: independent ext4 filesystems, with optional MergerFS pooling to provide one namespace without striping or mirroring.
- Kept active VM system disks on the internal NVMe; HDDs are intended for bulk data and local backup targets.
- Documented the proposed design separately from implementation. No formatting, mounting, or pool creation has occurred.

### Media storage design
- Selected the first 6 TB Seagate as a standalone media and application-data disk for a Debian VM running Jellyfin and supporting services such as Radarr and Sonarr.
- Planned physical disk passthrough to the Debian VM using a persistent `/dev/disk/by-id` path and ext4 inside the guest.
- Reserved the second 6 TB drive for later expansion. MergerFS and multi-disk pooling are no longer part of the initial setup.
- Kept the VM operating system and container configuration on the internal NVMe; media, downloads, and library data will share one filesystem in the guest.
- The storage design is selected but has not been implemented.

### Current state
- Proxmox VE is installed and accessible through the web interface; the displayed manager version will be rechecked after the post-upgrade reboot.
- One 6 TB Seagate Exos drive is installed in the D4-320; the second matching drive is awaiting delivery.
- The D4-320 drive is detected as `/dev/sda` and reports SMART overall status `PASSED`; detailed health checks and storage configuration remain pending.
