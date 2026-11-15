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

### Package repositories
- Disabled the Proxmox VE Enterprise and Ceph Squid Enterprise entries.
- Enabled the Proxmox VE no-subscription repository and Ceph Tentacle no-subscription repository.
- Retained the Debian Trixie and Debian Security repositories.
- Added documentation for the package source configuration. Package list refresh and update review are next.

### Current state
- Proxmox VE 9.2.2 is installed and accessible through the web interface.
- One 6 TB Seagate Exos drive is installed in the D4-320; the second matching drive is awaiting delivery.
- The D4-320 drive is detected as `/dev/sda` and reports SMART overall status `PASSED`; detailed health checks and storage configuration remain pending.
