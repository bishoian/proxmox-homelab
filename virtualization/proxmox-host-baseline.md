# Proxmox Host Baseline

**Project owner:** Ian Bishop  
**Host:** Lenovo ThinkCentre M920q Tiny  
**Proxmox release:** 9.2.2  
**Status:** Initial web access verified; hardware and storage inspection in progress.

## First dashboard access

After installation, I opened the Proxmox management interface from another device on my home network and successfully signed in.

The initial dashboard confirmed:

- The web interface loads successfully.
- The `pve` node is displayed with a green online indicator.
- The interface identifies the platform as Proxmox Virtual Environment 9.2.2.
- The node view reports six CPU cores.
- The initial dashboard showed low CPU utilization and approximately 10% memory utilization at the time of the screenshot.

These are initial observations, not performance benchmarks. I will capture a fuller hardware inventory from the node Summary and Shell views.

## Management access

The host is reachable through its HTTPS management interface on TCP port 8006 from the home LAN. The management address is maintained in my local network configuration.

## Storage connection

After verifying first web access, I connected the TerraMaster D4-320 to the Lenovo over USB. The enclosure is physically connected, but Proxmox disk detection, disk identity, SMART health, and mount configuration are still pending.

## Next validation tasks

1. Inspect **Node → Summary** and record CPU, RAM, uptime, and installed version.
2. Inspect **Node → Disks → Disks** to see whether the D4-320's installed Seagate drive is detected.
3. Identify the disk by model and capacity before considering any storage operation.
4. Review update repositories and apply available updates.
5. Plan the data disk's filesystem, mount point, permissions, and backup strategy before using it for media.

No disk initialization, formatting, partitioning, or storage-pool creation has been performed as part of this milestone.
