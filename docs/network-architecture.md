# Network Architecture

This document describes the homelab's current connection layout and the planned network design. Port assignments, link speeds, and switch configuration will be filled in as they are verified.

## Physical topology

```text
                         Internet
                            |
                    NETGEAR Nighthawk
                         RAX41v2
                            |
                      Ethernet uplink
                            |
                  RealHD managed switch
                   8 x 2.5GbE + 10G SFP+
                     /       |        \
                    /        |         \
          Lenovo M920q   Patch panel   Other LAN
          Proxmox host   / fixed runs   devices
               |
          USB connection
               |
        TerraMaster D4-320
          Direct-attached
             storage
```

The patch panel is a passive cabling termination/distribution point. It does not switch or route traffic. The exact relationship between the switch, patch panel, rack shelf pass-through ports, router, and room cabling will be recorded after the cables are traced.

The Proxmox host has been installed and its web interface is accessible. The D4-320 is physically connected to the Lenovo over USB; disk detection and health checks are pending.

## Equipment

| Device | Role | Notes |
|---|---|---|
| NETGEAR Nighthawk RAX41v2 | Existing home router / internet gateway | Current routing and DHCP behavior |
| RealHD 8-port 2.5GbE managed switch | Ethernet switching | Exact model/revision, firmware, management method, and configuration to be verified |
| GeeekPi 12-port CAT6 patch panel | Passive cable organization | No switching or routing functionality |
| Lenovo ThinkCentre M920q | Proxmox host | Built-in NIC link speed and driver to be verified |
| TerraMaster D4-320 | Direct-attached storage | Connected to Lenovo over USB; not Ethernet |

## Baseline network first

The initial deployment uses the existing home LAN without introducing VLANs or changing router-wide settings.

The Proxmox host was installed with a static management address and a reservation in the home router. The web interface is accessible from another device on the LAN.

Next, I will verify the host's interface status and negotiated link speed, then document the switch's management method and current port configuration.

## Planned segmentation (future)

VLANs are a later learning milestone, not part of the initial installation.

| Example VLAN | Intended use | State |
|---:|---|---|
| 10 | Infrastructure management | Proposed only |
| 20 | Server workloads | Proposed only |
| 30 | Trusted personal devices | Proposed only |
| 40 | Isolated lab/testing | Proposed only |

These VLAN IDs are examples, not configured networks. The router or a dedicated firewall must provide appropriate Layer 3 routing, DHCP, and firewall policy for devices in different VLANs. A managed switch alone does not automatically provide those services.

Before implementing VLANs, I will verify that the current router or a planned firewall supports the required VLAN interfaces and routing behavior. I will preserve a known-good management path and recovery method while testing.

## Link-speed expectations

The switch supports multi-gigabit ports, but an attached device only communicates at a speed supported by both ends of the link and the cabling. Record actual negotiated speed rather than using the switch's advertised maximum as proof of the Lenovo's connection speed.

The switch's 10G SFP+ port is an uplink option, not evidence that the Lenovo currently has a 10GbE interface.

## Port map (to be completed)

| Switch port | Connected device / destination | Negotiated speed | VLAN | Verified |
|---|---|---|---|---|
| 1 | TBD | TBD | Default LAN | No |
| 2 | TBD | TBD | Default LAN | No |
| 3 | TBD | TBD | Default LAN | No |
| 4 | TBD | TBD | Default LAN | No |
| 5 | TBD | TBD | Default LAN | No |
| 6 | TBD | TBD | Default LAN | No |
| 7 | TBD | TBD | Default LAN | No |
| 8 | TBD | TBD | Default LAN | No |
| SFP+ | TBD / unused | TBD | TBD | No |

Update this table after physically tracing and verifying each connection.

## Validation checklist

- [ ] Trace the router-to-switch uplink.
- [ ] Trace switch-to-patch-panel connections.
- [ ] Trace the Lenovo's Ethernet path through the rack shelf/pass-through, if used.
- [ ] Confirm link lights and negotiated speeds.
- [ ] Identify switch firmware and management access.
- [ ] Record switch port mappings.
- [x] Confirm Proxmox management interface is accessible from a LAN device.
- [ ] Confirm Proxmox detects the D4-320's installed drive.
- [ ] Plan VLANs only after the baseline LAN is stable.
