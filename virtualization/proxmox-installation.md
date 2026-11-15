# Proxmox VE Installation Notes

**Project owner:** Ian Bishop  
**Host:** Lenovo ThinkCentre M920q Tiny  
**Status:** Installed; web interface verification pending.

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

The management address and hardware MAC address are intentionally omitted from this public document. They are retained only in the local configuration and private network records.

## Installation result

The installer completed and the Lenovo rebooted successfully to the Proxmox local console. The console displayed the HTTPS web-management endpoint on TCP port 8006.

The first browser login has not yet been verified. The installed Proxmox VE version and host hardware inventory will be recorded after accessing the management interface.

## First access procedure

1. From another device connected to the same trusted home network, open a web browser.
2. Navigate to the HTTPS management URL displayed on the Proxmox host console.
3. Expect a browser certificate warning on the initial connection because the installation uses a self-signed certificate by default. Verify that the browser is connecting to the local host before proceeding.
4. Sign in as `root`.
5. Select the `Linux PAM standard authentication` realm (shown as `pam` in some interface versions).
6. Enter the root password created during installation. Never record this password in the repository.

## Post-install validation checklist

- [ ] Browser can reach the Proxmox web interface.
- [ ] Root authentication succeeds.
- [ ] Node appears online in the interface.
- [ ] Proxmox VE version is recorded.
- [ ] CPU and memory are recognized as expected.
- [ ] NVMe storage is visible and healthy.
- [ ] Network interface and link status are verified.
- [ ] Appropriate update repositories are configured.
- [ ] A backup plan is defined before creating important workloads.

## Lessons and operational notes

- The Proxmox host is managed through a web interface; a permanent monitor and keyboard are not required for normal administration after network access is verified.
- The D4-320 is USB direct-attached storage and should be commissioned separately after the Proxmox host baseline is stable.
- Public documentation must not contain passwords, hardware serial numbers, private keys, or exact private network details.
