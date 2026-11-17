# DNS Services Plan

I plan to add Dynamic DNS and Pi-hole to my homelab. Neither service is deployed yet.

## Dynamic DNS

I want Dynamic DNS to keep my public DNS record pointed at my home WAN address when that address changes. Jellyfin and Seerr already use Nginx Proxy Manager with verified HTTPS access, but I have not selected a DNS provider or update method.

- [ ] I will identify my authoritative DNS provider and confirm that it supports automated DNS updates.
- [ ] I will select and deploy an updater that can securely authenticate with the provider.
- [ ] I will test an update and confirm that the public record follows my current WAN address.
- [ ] I will document how I store updater credentials, monitor update status, and recover from failures without publishing secrets.

## Pi-hole

I plan to use Pi-hole for LAN DNS filtering and local DNS records. I will give it a stable LAN address and keep its DNS and administration services private to my home network.

My router currently provides DHCP. Before I enable Pi-hole's DHCP service, I will decide whether DHCP should remain on the router or move to Pi-hole. I will avoid running two uncoordinated DHCP servers on the same LAN.

- [ ] I will deploy Pi-hole on a stable LAN address.
- [ ] I will configure upstream DNS and select an initial set of blocklists.
- [ ] I will choose whether my router or Pi-hole provides DHCP.
- [ ] I will add local DNS records for my homelab services.
- [ ] I will test DNS resolution and filtering from a LAN client.
- [ ] I will document how I back up Pi-hole, apply updates, provide fallback DNS, and recover the service.

I will keep Pi-hole's DNS service and administration interface private to my LAN rather than exposing them directly to the internet.
