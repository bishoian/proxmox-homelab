# Changelog

Notable project documentation and infrastructure milestones are recorded here.

## 2026-11-15

### Added
- Expanded the project README with objectives, target architecture, milestone overview, and documentation practices.
- Added the initial hardware inventory, including the rack, power distribution, patch panel, switch, server, DAS enclosure, disks, and cabling.
- Added the planned network architecture and a placeholder switch port map for later verification.
- Added a project-status checklist separating completed preparation from planned and unverified work.

### Status
- Bootable Proxmox USB creation reported complete.
- Proxmox installation and configuration remain pending.
- One 6 TB Seagate Exos disk is installed; the second matching disk is awaiting delivery.


### Updated
- Recorded the Lenovo M920q BIOS configuration: Intel VT-x and Intel VT-d enabled and saved (user-reported).
- Updated the project README and milestone checklist. USB boot and Proxmox installation remain pending.

### Updated
- Recorded that the Lenovo successfully booted from the Proxmox USB installer. Disk selection and installation remain pending.

### Added
- Documented the initial Proxmox VE installation on the Lenovo M920q's internal NVMe and first boot to the local console.

### Status
- Proxmox VE installation completed; first web-interface login remains to be verified.


### Added
- Recorded first successful login to the Proxmox VE 9.2.2 web interface and confirmed the `pve` node is online.
- Added an initial Proxmox host baseline record.
- Recorded the TerraMaster D4-320 as physically connected to the Lenovo over USB; disk detection and health checks remain pending.

### Updated
- Updated the README, project checklist, hardware inventory, network architecture, and installation notes to reflect the verified dashboard access and current storage connection.
- Removed repetitive publication/security reminders from supporting documentation, keeping the general documentation practice on the main README.
