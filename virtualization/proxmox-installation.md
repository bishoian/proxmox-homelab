# Proxmox VE Installation

**Host:** Lenovo ThinkCentre M920q Tiny  
**Installed release:** Proxmox VE 9.2.2 (installation media)  
**Current package state:** Updated; see [Host Baseline](proxmox-host-baseline.md).

## Preparation

I enabled Intel VT-x and VT-d in BIOS, disconnected the TerraMaster DAS and data disks to avoid selecting the wrong installation target, and created a bootable Proxmox VE installer USB.

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

The installer completed and I rebooted the host to the Proxmox console. I opened the web interface from another device on my home LAN, authenticated as root through the Linux PAM realm, and confirmed that the `pve` node appeared online.

## Verified installation tasks

- [x] I enabled VT-x and VT-d in BIOS.
- [x] I installed Proxmox VE to the internal NVMe.
- [x] I rebooted to the local console.
- [x] I accessed the web interface and authenticated.
- [x] I confirmed that the `pve` node was online.
- [x] I confirmed that the installation release was Proxmox VE 9.2.2.
- [x] I verified the CPU, memory, NVMe layout, and network link in my host baseline.
- [x] I configured package repositories, applied upgrades, and rebooted into the updated kernel.

Current kernel and package versions are recorded in [Proxmox Host Baseline](proxmox-host-baseline.md). VM and storage commissioning are documented separately.
