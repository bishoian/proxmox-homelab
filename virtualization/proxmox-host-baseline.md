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

The installed processor is the standard i5-8500, not the i5-8500T. The M920q's onboard Ethernet is Gigabit Ethernet, so the current 1 Gbps link is expected even though the RealHD switch supports 2.5GbE.

## Storage layout

The internal NVMe contains the Proxmox EFI and LVM layout:

- 1 GB EFI partition mounted at `/boot/efi`.
- 8 GB swap volume.
- Approximately 69.4 GB root filesystem.
- LVM-thin data pool for VM disks.

The Seagate Exos is passed through to Debian VM 100 and is managed by the guest. See [Media Storage Design](../storage/storage-layout.md).

## Network baseline

| Interface | State | Speed | Role |
|---|---|---:|---|
| `nic0` | UP | 1000 Mb/s | Physical Ethernet |
| `vmbr0` | UP | 1000 Mb/s | Proxmox bridge |
| `wlo1` | DOWN | Not applicable | Wireless |
| `lo` | UP | Not applicable | Loopback |

The Proxmox web interface is reachable from the home LAN on TCP 8006. The management address is kept in local network configuration rather than published here.

## Package sources and updates

Enabled sources are Debian Trixie and Trixie Security, Proxmox VE no-subscription, and Ceph Tentacle no-subscription. Proxmox VE Enterprise and Ceph Squid Enterprise entries are disabled. No Ceph cluster is deployed.

After package refresh, upgrades, and reboot, the following versions were verified:

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

- [ ] Record BIOS/UEFI version and RAM module layout.
- [ ] Review detailed NVMe health attributes.
- [ ] Document host recovery and console access.
- [ ] Establish a maintenance and backup routine.
