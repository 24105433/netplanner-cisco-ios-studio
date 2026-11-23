# NetPlanner & Cisco IOS Studio

One offline HTML file that plans IPv4/IPv6 addressing (VLSM) and writes the Cisco IOS commands for each router, ready to paste into PuTTY or Packet Tracer.

Open `index.html` in any browser. No install, no internet needed.

## What it does

| Tab | Use it for |
|---|---|
| 1. Addressing | Base network + host counts → VLSM subnet table, paired IPv6 /64s, LAN and WAN rows linked to router ports |
| 2. Topology & CLI | Live diagram, per-router config: passwords, SSH/Telnet, interfaces, DHCPv4/DHCPv6, routing, ACLs |
| 3. Help | Exam workflow, what to type in each field, PuTTY console, router reset, password recovery, cables, PC setup |
| 4. Troubleshooting | Symptom → check → fix, IOS messages, show commands by topic (click to copy) |

## Features

- Routers: Cisco 2901 (g0/0–g0/1) and 2911 (g0/0–g0/2), serial ports s0/0/0–s0/1/1 with clock rate, loopbacks
- Routing: static, RIPv2, OSPF, EIGRP per router; border routers redistribute with seed metrics; router IDs; floating and IPv6 static routes
- IPv6: auto static routes, RIPng, or the same protocol as IPv4 (OSPFv3, EIGRP for IPv6)
- DHCP: local or relayed (ip helper-address), exclusions, DNS, domain name, lease; DHCPv6 stateless/stateful
- ACLs written as plain rules ("block LAN A from Telnet to LAN B"), placed and numbered automatically
- **Copy in parts**: the script split into small blocks with Copy buttons and ticks, so a 9600-baud console paste does not drop characters
- **Verify**: show and ping commands for the selected router
- Router CSV import/export (`router,interface,ipv4,ipv6,link_local`)
- Save / load the whole project as `.json`

## Quick start

1. **Addressing:** type the base network and prefix, add routers, add one LAN row per LAN and one WAN row per router-to-router link, pick the router and port for each row.
2. **Topology & CLI:** set passwords and remote access in Init Config, DHCP per LAN, and one routing protocol per router.
3. **CLI pane:** pick a router, copy each part, right-click in PuTTY to paste, wait for the prompt, next part.
4. **Verify:** run the Verify commands; use the Troubleshooting tab if something fails.
