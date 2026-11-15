# Proxmox Host Baseline

**Project owner:** Ian Bishop  
**Host:** Lenovo ThinkCentre M920q Tiny  
**Proxmox release:** 9.2.2  
**Status:** Web interface accessible; hardware and storage inspection in progress.

## First dashboard access

The Proxmox management interface was opened from another device on the home network, and the initial login succeeded.

The dashboard confirmed:

- The web interface loads successfully.
- The `pve` node is displayed with a green online indicator.
- The platform is Proxmox Virtual Environment 9.2.2.
- The node view reports six CPU cores.
- At the time of the screenshot, CPU utilization was approximately 0.2% and memory utilization was approximately 10.2%.

These figures are initial observations, not performance benchmarks. A subsequent Node → Summary inspection reported:

| Summary field | Displayed value |
|---|---:|
| Uptime at capture | 1 hour, 4 minutes |
| CPU usage | 0.08% of 6 CPUs |
| Load average | 0.11 / 0.04 / 0.01 |
| CPU label | Intel Core i5-8500 @ 3.00 GHz, 1 socket |
| Memory | 1.65 GiB of 15.46 GiB (10.66%) |
| Root filesystem | 4.14 GiB of 67.73 GiB (6.11%) |
| Swap | 0 B of 8.00 GiB |
| Kernel | Linux 7.0.2-6-pve |
| Boot mode | EFI |
| Manager | pve-manager 9.2.2 |

The processor was subsequently confirmed as the standard Intel Core i5-8500, not the i5-8500T originally listed. The hardware inventory now reflects the installed CPU. The 67.73 GiB figure is the dashboard's root filesystem capacity, not the advertised capacity of the 256 GB NVMe; the installer also created separate LVM storage.

## CPU and network interface verification

The Proxmox Shell reported the following CPU details:

| Field | Result |
|---|---|
| Model | Intel Core i5-8500 CPU @ 3.00 GHz |
| Socket count | 1 |
| Logical CPUs | 6 (online CPUs 0–5) |

The interface status and link-speed checks reported:

| Interface | State | Link speed | Notes |
|---|---|---:|---|
| `nic0` | UP | 1000 Mb/s | Physical Ethernet interface |
| `vmbr0` | UP | 1000 Mb/s | Proxmox bridge using the physical uplink |
| `wlo1` | DOWN | Not reported | Wireless interface, not used for host connectivity |
| `lo` | UP | Not applicable | Loopback interface |

The host's wired network link is currently 1 Gbps. Although the RealHD switch supports 2.5GbE, the active link negotiates at 1 Gbps. The physical connection path and switch-side port status will be checked separately once the rack cabling is traced.

## Management access

The host is reachable through its HTTPS management interface on TCP port 8006 from the home LAN. The management address is recorded in the local network configuration.

## Storage connection

The TerraMaster D4-320 is connected to the Lenovo over USB. The enclosure's rear power switch was initially off, so the drive did not appear in Proxmox. After switching the enclosure on and refreshing the disk list, Proxmox detected the 6 TB Seagate Exos as `/dev/sda`. The disk view reports SMART overall status `PASSED`. The initial attribute table shows zero reallocated, pending, offline-uncorrectable, reported-uncorrectable, and UDMA CRC error counts. The reported temperature is 24°C. The power-on-hours raw value is 0, while the head-flying-hours counter shows approximately 4 minutes and 17 seconds. These values are recorded as displayed; an extended SMART self-test remains pending.

## Package repositories

The package source configuration uses the Debian Trixie base and security repositories, the Proxmox VE no-subscription repository, and the Ceph Tentacle no-subscription repository. The Proxmox VE Enterprise and older Ceph Squid Enterprise entries are disabled. The repository interface displays informational warnings that no-subscription channels are not recommended for production use; this is a personal homelab environment. No Ceph cluster has been deployed.

See [Package Repository Configuration](../docs/package-repositories.md) for the configured sources.

## Package database refresh

The package database refresh completed successfully. The task output showed successful access to the Debian Trixie, Debian Security, Proxmox VE no-subscription, and Ceph Tentacle no-subscription repositories. A total of 530 kB was fetched, package lists were read, and the task ended with `TASK OK`. The available upgrades were reviewed and installed successfully. The Updates view now reports no available updates. The batch included a newer Proxmox kernel and manager package. The host has not yet been rebooted, so the active kernel and running manager version remain to be verified.

## Package upgrade

The available upgrades were applied without reported errors. The Proxmox Updates view now reports `No updates available`. The update batch included a newer Proxmox kernel and manager package. A reboot is pending; the active kernel and running Proxmox manager version will be checked after reboot. The reboot is being held until the extended SMART test on the Seagate finishes.

## Next validation tasks

1. Inspect **Node → Summary** and record CPU, RAM, uptime, and installed version.
2. Run an extended SMART self-test and review the result.
3. Allow the Seagate extended SMART self-test to finish.
4. Reboot to activate the updated kernel.
5. Verify the active kernel, Proxmox manager version, and node health after reboot.
6. Record the NVMe model and health.
7. Plan the data disk's filesystem, mount point, permissions, and backup strategy before using it for media.

No disk initialization, formatting, partitioning, or storage-pool creation has been performed as part of this milestone.
