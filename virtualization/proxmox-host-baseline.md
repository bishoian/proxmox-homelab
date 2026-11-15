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

These figures are initial observations, not performance benchmarks. A fuller hardware inventory will be captured from the node Summary and Shell views.

## Management access

The host is reachable through its HTTPS management interface on TCP port 8006 from the home LAN. The management address is recorded in the local network configuration.

## Storage connection

The TerraMaster D4-320 is connected to the Lenovo over USB. The enclosure's rear power switch was initially off, so the drive did not appear in Proxmox. After switching the enclosure on and refreshing the disk list, Proxmox detected the 6 TB Seagate Exos as `/dev/sda`. The disk view reports SMART overall status `PASSED`. Detailed SMART attributes, power-on hours, and an extended self-test remain pending.

## Next validation tasks

1. Inspect **Node → Summary** and record CPU, RAM, uptime, and installed version.
2. Review the Seagate's detailed SMART attributes and power-on hours.
3. Run an extended SMART self-test and review the result.
4. Review update repositories and apply available updates.
5. Plan the data disk's filesystem, mount point, permissions, and backup strategy before using it for media.

No disk initialization, formatting, partitioning, or storage-pool creation has been performed as part of this milestone.
