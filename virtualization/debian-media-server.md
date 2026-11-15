# Debian Media Server VM Build

**VM ID:** 100  
**VM name:** `media-server`  
**Hypervisor:** Proxmox VE on Lenovo ThinkCentre M920q Tiny  
**Guest OS:** Debian GNU/Linux 13.7 (Trixie), amd64  
**Build status:** Debian installation completed; first boot and login verification pending.

## VM configuration

| Setting | Value |
|---|---|
| vCPU | 1 socket, 4 cores |
| CPU type | `host` |
| Memory | 4096 MiB |
| System disk | 64 GiB on `local-lvm` |
| Disk bus | SCSI, VirtIO SCSI single |
| Disk options | Discard, IO thread, SSD emulation enabled |
| Firmware | SeaBIOS |
| Machine | Default i440fx |
| QEMU Guest Agent | Enabled in VM configuration; guest package still needs installation |
| Network | VirtIO NIC on `vmbr0`, Proxmox firewall enabled |
| Installer media | Debian 13.7 amd64 netinst ISO |

## Debian installer choices

- Hostname: `media-server`
- Domain name: left blank
- Root password: left blank, allowing Debian to lock direct root login and use the regular account with sudo privileges
- Network: installer configured through the VM's VirtIO network interface
- Disk partitioning: guided, all files in one partition with a separate swap area
- Desktop environment: not selected
- SSH server: selected
- Standard system utilities: selected
- GRUB bootloader: installed to the VM's virtual system disk

## Guest disk layout

The Debian installer detected one approximately 68.7 GB QEMU virtual disk. The physical Seagate disk was not attached to this VM during installation.

| Partition | Approximate size | Filesystem / role | Mount |
|---|---:|---|---|
| Primary partition | 65.2 GB | ext4 | `/` |
| Logical partition | 3.5 GB | swap | swap |

The partition changes were written only to the VM's virtual system disk. The Seagate Exos data disk remains on the Proxmox host and has not been passed through, partitioned, or formatted.

## Installation result

Debian displayed the installer completion screen after the base system and selected packages had been installed. GRUB installation was confirmed for the VM's virtual disk.

The VM has not yet been verified after its first boot. Before rebooting, detach the Debian installer ISO from the VM's virtual CD/DVD drive so the guest boots from its installed system disk.

## First-boot checklist

- [ ] Set the VM's CD/DVD drive to **Do not use any media** in Proxmox Hardware.
- [ ] Reboot VM 100.
- [ ] Confirm Debian reaches its login prompt.
- [ ] Log in with the regular account created during installation.
- [ ] Confirm the guest obtained a LAN IP address.
- [ ] Verify DNS and internet connectivity.
- [ ] Verify SSH access from another LAN device.
- [ ] Install the `qemu-guest-agent` package and enable its service.
- [ ] Apply Debian updates.
- [ ] Record the guest's actual kernel, IP address, and package baseline.

## Planned media storage

The 6 TB Seagate Exos will be passed through to this Debian VM after first-boot configuration. It will be formatted as ext4 inside Debian and mounted for media, downloads, and application data. The second 6 TB drive remains reserved for future expansion.
