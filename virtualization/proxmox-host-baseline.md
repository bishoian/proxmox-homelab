# Proxmox Host Baseline

**Host:** Lenovo ThinkCentre M920q Tiny  
**Node:** `pve`  
**Current kernel:** `7.0.14-29-pve`  
**Proxmox manager:** `pve-manager 9.2.27`

## Hardware summary

| Resource | Verified value |
|---|---|
| CPU | Intel Core i5-8500, 1 socket, 6 logical CPUs |
| Memory | 15.46 GiB usable |
| NVMe | Lenovo NVMe 2280S 256 GB; Linux reports 238.5 GB |
| Boot mode | EFI |
| Ethernet | `nic0` and `vmbr0` up at 1 Gbps |
| Wireless | `wlo1`, not used |

I confirmed that the installed processor is the standard i5-8500, not the i5-8500T. The M920q's onboard Ethernet is Gigabit Ethernet, so I expect a 1 Gbps link even though my RealHD switch supports 2.5GbE.

## Storage layout

I installed Proxmox on the internal NVMe, which contains the following EFI and LVM layout:

- 1 GB EFI partition mounted at `/boot/efi`.
- 8 GB swap volume.
- Approximately 69.4 GB root filesystem.
- LVM-thin data pool for VM disks.

I pass the Seagate Exos through to Debian VM 100 and manage its filesystem from the guest. See [Media Storage Design](../storage/storage-layout.md).

## Network baseline

| Interface | State | Speed | Role |
|---|---|---:|---|
| `nic0` | UP | 1000 Mb/s | Physical Ethernet |
| `vmbr0` | UP | 1000 Mb/s | Proxmox bridge |
| `wlo1` | DOWN | Not applicable | Wireless |
| `lo` | UP | Not applicable | Loopback |

I access the Proxmox web interface from my home LAN on TCP 8006. I keep its management address in my local network configuration rather than publishing it here.

## Package sources and updates

I enabled Debian Trixie and Trixie Security, Proxmox VE no-subscription, and Ceph Tentacle no-subscription sources. I disabled the Proxmox VE Enterprise and Ceph Squid Enterprise entries. I have not deployed a Ceph cluster.

After refreshing package lists, applying upgrades, and rebooting, I verified the following versions:

| Package / field | Version |
|---|---|
| Running kernel | `7.0.14-29-pve` |
| `pve-manager` | `9.2.27` |
| `proxmox-ve` | `9.2.0` |
| `proxmox-kernel-7.0` | `7.0.14-29` |
| `pve-qemu-kvm` | `11.1.3-1` |
| `qemu-server` | `9.2.15` |
| `ceph-fuse` | `20.2.5-pve1` |

The no-subscription repositories are used for this personal lab, not as a production support arrangement. Repository details are in [Package Repository Configuration](../docs/package-repositories.md).

## Remaining host tasks

- [ ] I will record the BIOS/UEFI version and RAM module layout.
- [ ] I will review the NVMe's detailed health attributes.
- [ ] I will document my host recovery and console-access process.
- [ ] I will establish a maintenance and backup routine.
