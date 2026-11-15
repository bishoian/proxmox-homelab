# Proxmox VE Installation Notes

**Project owner:** Ian Bishop  
**Host:** Lenovo ThinkCentre M920q Tiny  
**Status:** Installed; web interface login verified.

## Objective

Install Proxmox VE as the bare-metal hypervisor on the Lenovo ThinkCentre M920q to host virtual machines and Linux containers for the homelab.

## Hardware and preparation

- Lenovo ThinkCentre M920q Tiny.
- Intel Core i5-8500T.
- 16 GB RAM.
- 256 GB NVMe SSD.
- Wired Ethernet connection.
- Bootable Proxmox VE USB installer.

Intel Virtualization Technology (VT-x) and Intel VT-d were enabled in BIOS before installation. The TerraMaster DAS and external data drives were disconnected to reduce the chance of selecting the wrong installation disk.

## Installer choices

| Installer setting | Selected value |
|---|---|
| Target disk | Internal NVMe drive (installer identified it as `/dev/nvme0n1`) |
| Filesystem | ext4 |
| Country | United States |
| Time zone | America/Chicago |
| Keyboard layout | en-us |
| Management interface | `nic0` (Intel Ethernet interface shown by installer) |
| Hostname | `pve` |
| Network addressing | Static IPv4 configuration with a router reservation |
| Gateway and DNS | Existing home router |

## Installation result

The installer completed and the Lenovo rebooted successfully to the Proxmox local console. The console displayed the HTTPS web-management endpoint on TCP port 8006.

The management endpoint was opened from another device on the home network, and authentication succeeded using the root account and Linux PAM authentication.

The dashboard displayed the `pve` node as online and identified the installed release as **Proxmox Virtual Environment 9.2.2**.

## First access procedure

1. From another device on the home network, open a web browser.
2. Navigate to the HTTPS management address displayed on the Proxmox host console.
3. On the initial connection, expect a browser certificate warning because Proxmox uses a self-signed certificate by default.
4. Sign in as `root`.
5. Select the `Linux PAM standard authentication` realm (shown as `pam` in some interface versions).

## Post-install validation

- [x] Browser can reach the Proxmox web interface.
- [x] Root authentication succeeds.
- [x] Node appears online in the interface.
- [x] Proxmox VE version is visible in the interface (9.2.2).
- [ ] CPU and memory are recognized as expected.
- [ ] NVMe storage is visible and healthy.
- [ ] Network interface and link status are verified.
- [ ] Review update repositories and apply available updates.
- [ ] Define a backup plan before creating important workloads.

## Operational notes

- The Proxmox host is managed through a web interface; a permanent monitor and keyboard are not required for normal administration after network access is established.
- The D4-320 is USB direct-attached storage. It is connected to the Lenovo and will be commissioned after the installed disk is detected and checked.
