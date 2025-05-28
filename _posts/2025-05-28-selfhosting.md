---
layout: post
title: Selfhosting behind NAT with Wireguard and Caddy
lead: Selfhosting behind NAT
---

For a long time, I was fortunate to have a publicly available IPv4 address, which allowed me to use Cloudflare to access my services remotely. Unfortunately, my ISP changed this, and Cloudflare (via ddclient for IP updates) started responding with "Bad Request" because my IP was no longer public.

So I set out to find a new way to regain access.

I was already using Tailscale to access my LAN while on the move. However, this was more of a manual, on-demand solution, as the battery drain was quite high. Therefore, Tailscale was not an option for an always-on connection.

The next best approach was to rent a small VPS, establish a VPN connection to my homelab, install a reverse proxy on the VPS, and point Cloudflare to my VPS. For the VPN, I chose Wireguard due to its easy configuration, low resource usage, and modern cryptography. As a reverse proxy, I chose Caddy for similar reasons: simple configuration and minimal resource requirements.

The setup was straightforward. I followed the [Wireguard documentation](https://www.wireguard.com/quickstart/) to establish the VPN connection between my homelab and the VPS. Instead of a bare-metal installation, I opted to run Wireguard in Docker to separate my services using Docker networks.  
For Caddy, I needed two instances: one on the VPS and one in my homelab. On the VPS, I configured Caddy to serve only my domains and proxy requests to the Caddy instance in my homelab. The homelab Caddy instance is set up to communicate with other containers in the Wireguard network. Caddy's documentation covers this setup under [Proxying to another Caddy](https://caddyserver.com/docs/caddyfile/patterns#caddy-proxying-to-another-caddy). The best part is that Caddy automatically handles TLS certificates, so I don't have to worry about renewing them.