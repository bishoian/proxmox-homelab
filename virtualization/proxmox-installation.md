# Proxmox VE Installation Notes

**Project owner:** Ian Bishop  
**Host:** Lenovo ThinkCentre M920q Tiny  
**Status:** Installed; web interface login verified.

## Objective

Install Proxmox VE as the bare-metal hypervisor on the Lenovo ThinkCentre M920q so the system can host virtual machines and Linux containers for the homelab.

## Hardware and preparation

- Lenovo ThinkCentre M920q Tiny.
- Intel Core i5-8500T.
- 16 GB RAM.
- 256 GB NVMe SSD.
- Wired Ethernet connection.
- Bootable Proxmox VE USB installer.

Before installation, I enabled Intel Virtualization Technology (VT-x) and Intel VT-d in BIOS. I disconnected the TerraMaster DAS and external data drives to reduce the chance of selecting the wrong disk.

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

I opened the management endpoint from another device on the home network and successfully authenticated to the Proxmox web interface using the root account and Linux PAM authentication.

The dashboard displayed the `pve` node as online. The interface identifies the installed release as **Proxmox Virtual Environment 9.2.2**.

## First access procedure

1. From another device connected to the same trusted home network, open a web browser.
2. Navigate to the HTTPS management URL displayed on the Proxmox host console.
3. Expect a browser certificate warning on the initial connection because the installation uses a self-signed certificate by default. Verify that the browser is connecting to the local host before proceeding.
4. Sign in as `root`.
5. Select the `Linux PAM standard authentication` realm (shown as `pam` in some interface versions).
6. Enter the root password created during installation. Never record this password in the repository.

## Post-install validation checklist

- [x] Browser can reach the Proxmox web interface.
- [x] Root authentication succeeds.
- [x] Node appears online in the interface.
- [x] Proxmox VE version is visible in the interface (9.2.2).
- [ ] CPU and memory are recognized as expected.
- [ ] NVMe storage is visible and healthy.
- [ ] Network interface and link status are verified.
- [ ] Review update repositories and apply available updates.
- [ ] Define a backup plan before creating important workloads.

## Lessons and operational notes

- The Proxmox host is managed through a web interface; a permanent monitor and keyboard are not required for normal administration after network access is verified.
- The D4-320 is USB direct-attached storage. It has now been connected to the Lenovo and will be commissioned separately after its disk is detected and checked.
