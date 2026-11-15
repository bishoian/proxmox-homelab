# Network Architecture

This document describes the intended network layout for the homelab. It is deliberately a design document until the actual cabling, addressing, link speeds, and switch configuration have been inspected.

## Physical topology (planned)

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

The patch panel is a passive cabling termination/distribution point. It does not switch or route traffic. The exact relationship between the switch, patch panel, rack shelf pass-through ports, router, and room cabling must be verified and then reflected in an updated diagram.

The TerraMaster D4-320 connects to the Lenovo over USB. It does not connect to the Ethernet switch.

## Equipment

| Device | Role | Notes |
|---|---|---|
| NETGEAR Nighthawk RAX41v2 | Existing home router / internet gateway | Current routing and DHCP behavior to be inspected |
| RealHD 8-port 2.5GbE managed switch | Ethernet switching | Exact model/revision, firmware, management method, and configuration to be verified |
| GeeekPi 12-port CAT6 patch panel | Passive cable organization | No switching or routing functionality |
| Lenovo ThinkCentre M920q | Planned Proxmox host | Built-in NIC link speed and driver to be verified |
| TerraMaster D4-320 | Direct-attached storage | USB connection to the Lenovo, not Ethernet |

## Baseline network first

The initial deployment should use the existing home LAN without introducing VLANs or changing router-wide settings.

Before changing any configuration:

1. Record the router's LAN subnet, gateway, and DHCP range privately.
2. Identify the switch management method and reset/recovery procedure from its documentation.
3. Identify the switch's current management address and firmware.
4. Confirm the Lenovo can obtain a DHCP lease and reach the router.
5. Record the Lenovo's negotiated link speed and interface name.
6. Reserve a management address for Proxmox in the router, or select a static address outside the DHCP pool after checking for conflicts.
7. Verify the Proxmox web interface from another trusted LAN device.

Do not publish the actual home subnet, gateway, management address, Wi-Fi details, or device MAC addresses in this public repository. Use placeholders in examples.

## Planned segmentation (future)

VLANs are a later learning milestone, not part of the initial installation.

| Example VLAN | Intended use | State |
|---:|---|---|
| 10 | Infrastructure management | Proposed only |
| 20 | Server workloads | Proposed only |
| 30 | Trusted personal devices | Proposed only |
| 40 | Isolated lab/testing | Proposed only |

These VLAN IDs are examples, not configured networks. The router or a dedicated firewall must provide appropriate Layer 3 routing, DHCP, and firewall policy for devices in different VLANs. A managed switch alone does not automatically provide those services.

Before implementing VLANs, verify that the current router or a planned firewall supports the required VLAN interfaces and routing behavior. Preserve a known-good management path and recovery method so an incorrect VLAN change does not lock out the switch or Proxmox host.

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

Update this table only after physically tracing and verifying each connection. Avoid recording sensitive MAC addresses or private addressing in this public file.

## Validation checklist

- [ ] Trace the router-to-switch uplink.
- [ ] Trace switch-to-patch-panel connections.
- [ ] Trace the Lenovo's Ethernet path through the rack shelf/pass-through, if used.
- [ ] Confirm link lights and negotiated speeds.
- [ ] Identify switch firmware and management access.
- [ ] Record a sanitized port map.
- [ ] Confirm Proxmox management access from a trusted device.
- [ ] Plan VLANs only after the baseline LAN is stable.
