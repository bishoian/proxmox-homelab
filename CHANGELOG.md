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
- Left disk detection, SMART checks, and storage configuration for the next commissioning stage.

### Current state
- Proxmox VE 9.2.2 is installed and accessible through the web interface.
- One 6 TB Seagate Exos drive is installed in the D4-320; the second matching drive is awaiting delivery.
- The D4-320 is connected, but disk detection and health checks are still pending.
