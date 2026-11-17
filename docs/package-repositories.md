# Package Repository Configuration

**Host:** Proxmox VE 9.2.2  
**Base distribution:** Debian 13 (Trixie)  
**Status:** Repository refresh, package upgrade, and post-upgrade reboot completed; running versions verified.

## Enabled repositories

| Repository | Distribution / suite | Component | Status |
|---|---|---|---|
| Debian base | Trixie and Trixie Updates | Main and configured Debian components | Enabled |
| Debian Security | Trixie Security | Main and configured Debian components | Enabled |
| Proxmox VE | Trixie | `pve-no-subscription` | Enabled |
| Ceph | Trixie | Ceph Tentacle `no-subscription` | Enabled |

Configured Proxmox source endpoints:

- Proxmox VE: `http://download.proxmox.com/debian/pve`
- Ceph Tentacle: `http://download.proxmox.com/debian/ceph-tentacle`

## Disabled repositories

| Repository | Reason |
|---|---|
| Proxmox VE Enterprise | Requires an active subscription |
| Ceph Squid Enterprise | Requires an active subscription; older Ceph release channel |

I use the Proxmox VE and Ceph no-subscription channels for this personal homelab. Proxmox warns that these channels are not recommended for production environments.

I configured the Ceph repository so packages are available for future learning, but I have not installed a Ceph cluster and do not use Ceph in my current single-node storage design.

## Validation record

- [x] I refreshed package lists from **Node → Updates → Refresh**.
- [x] I confirmed that Debian, Proxmox VE no-subscription, and Ceph Tentacle no-subscription sources were reached and their package lists were read successfully.
- [x] I reviewed the available package list.
- [x] I applied the available package upgrades.
- [x] I confirmed that the Updates view reported no remaining upgrades.
- [x] I rebooted after package upgrades to activate the updated kernel.
- [x] I verified running kernel `7.0.14-29-pve` and `pve-manager` `9.2.27`.

The refresh task completed with `TASK OK` and reported 530 kB fetched. I applied the available upgrades without reported errors. The Updates view then reported no remaining updates. After rebooting, I verified that the host was running kernel `7.0.14-29-pve` and `pve-manager` `9.2.27`.
