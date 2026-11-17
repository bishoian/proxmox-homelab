# Proxmox VE Installation

**Host:** Lenovo ThinkCentre M920q Tiny  
**Installed release:** Proxmox VE 9.2.2 (installation media)  
**Current package state:** Updated; see [Host Baseline](proxmox-host-baseline.md).

## Preparation

- Enabled Intel VT-x and VT-d in BIOS.
- Disconnected the TerraMaster DAS and data disks to avoid selecting the wrong installation target.
- Created a bootable Proxmox VE installer USB.

## Installer configuration

| Setting | Selection |
|---|---|
| Target disk | Internal NVMe (`/dev/nvme0n1`) |
| Filesystem | ext4 |
| Location | United States, `America/Chicago` |
| Keyboard | en-us |
| Management interface | `nic0` |
| Hostname | `pve` |
| Network | Static IPv4 with a router reservation |
| Gateway and DNS | Home router |

## Installation and first login

The installer completed, and the host rebooted to the Proxmox console. The web interface was opened from another device on the home LAN. Root authentication through the Linux PAM realm succeeded, and the `pve` node appeared online.

## Verified installation tasks

- [x] Enable VT-x and VT-d in BIOS.
- [x] Install Proxmox VE to the internal NVMe.
- [x] Reboot to the local console.
- [x] Access the web interface and authenticate.
- [x] Confirm the `pve` node is online.
- [x] Confirm the installation release is Proxmox VE 9.2.2.
- [x] Verify CPU, memory, NVMe layout, and network link in the host baseline.
- [x] Configure package repositories, apply upgrades, and reboot into the updated kernel.

Current kernel and package versions are recorded in [Proxmox Host Baseline](proxmox-host-baseline.md). VM and storage commissioning are documented separately.
