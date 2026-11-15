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

The TerraMaster D4-320 is connected to the Lenovo over USB. Proxmox disk detection, disk identity, SMART health, and mount configuration have not yet been checked.

## Next validation tasks

1. Inspect **Node → Summary** and record CPU, RAM, uptime, and installed version.
2. Inspect **Node → Disks → Disks** to see whether the D4-320's installed Seagate drive is detected.
3. Identify the disk by model and capacity before any storage operation.
4. Review update repositories and apply available updates.
5. Plan the data disk's filesystem, mount point, permissions, and backup strategy before using it for media.

No disk initialization, formatting, partitioning, or storage-pool creation has been performed as part of this milestone.
