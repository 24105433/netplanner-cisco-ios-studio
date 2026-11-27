# NetPlanner: CPE 3105 Final Skills-Based Lab Assessment mode

`index.html` is a single-file, offline app (NetPlanner & Cisco IOS Studio): Addressing (VLSM, IPv6), Topology & CLI generator, ACLs, Help, Troubleshooting. It is used during a **physical** lab exam: real 1941/2911 routers, 2960 switches, PCs, PuTTY over console.

Goal: make it guide Mal step by step through the assessment described in `Final_Skills_Based_Laboratory_Assessment.pdf` (read it first; it's 2 pages).

**These are background notes from an earlier chat.** The prompt Mal pastes decides what to do and in what order. The suggestions below are ideas to weigh, not orders; Mal's own answers are under "Decisions" and "ACL and access decisions".

## Ground rules
- Keep it ONE html file, no CDN, no external fonts or libraries. It must work offline on a lab PC.
- Don't break existing features. Old saved project .json files must still load: every new state field needs a default in the load/normalize code (see how `vtyLast` is normalized around line 4444).
- Plain short English in help text, `code.cmd` click-to-copy for commands (match the existing Help and Troubleshooting tabs).
- After each change, test the page with the Playwright MCP: open it, click every tab, load the Sample Lab, load the new preset, check the console for errors.
- No Packet Tracer. The deliverable is the HTML helper only; test it with the Playwright MCP.
- If the draw.io plugin is installed: draw the assessment topology (PDF Section I) as `topology.drawio`, with each router port's name and IPv4/IPv6 address from the app's subnet table, and export a PNG to print and keep beside the routers.
- Work in small commits, one change per commit.
- The gap list below came from a review in a chat session. Verify each item yourself against the code; don't trust it blindly.

## The assessment (from the PDF)

**Devices:** Edge Router (2911), Main Router (2911), R1, R2, R3 (1941), Switch0 to Switch3 (2960, left unconfigured), PCs.

**Links:**
- LANs: R1 to Switch0 (Registrar: PC0, PC1), Main to Switch1 (Finance/Accounting: PC2, PC3), R3 to Switch3 (Laboratories: PC4, PC5), R2 to Switch2 (Downtown Campus: PC8, PC9)
- WANs: R1 to Main, Main to Edge, Edge to R2, Edge to R3

**Addressing:** IPv4 `12G#.2S#.224.0/20`, IPv6 `200G#:DBS#:ACAD::/64` (G# = group number, S# = student number). Example G1, S1: `121.21.224.0/20` and `2001:db1:acad::`. Host counts per LAN are not in the PDF (given on the day).

**Address services:**
- DHCPv4: hosted on Edge Router (it has no LAN, so every LAN router needs `ip helper-address` pointing to Edge)
- IPv6: SLAAC on R1, SLAAC + DHCPv6 stateless on R2, stateful DHCPv6 on R3

**Initial config:** all routers. `line vty 0 4` = Telnet, `line vty 5 15` = SSH, password `GROUP5CISCO`.

**Routing:** R1 and Main = EIGRP. R2 = RIPv2. Edge and R3 = default static route. Redistribution is graded.

**ACLs:**
- a) Telnet: only PC0, PC2, PC4 may Telnet to all internal routers. Everyone else is denied. Login must still work.
- b) SSH: only PC8, PC9 may SSH to the "ISP Router" and Edge Router. Everyone else is denied.
- c) Laboratory is denied to Registrar and Finance. Finance is denied to Registrar. Registrar may initiate to Laboratory. Other services keep working.
- d) Downtown and Laboratory may talk to each other. Downtown is denied to Registrar and Finance.

**Wireshark (section IV):**
- ICMP: capture a ping between PCs in different LANs; show Echo Request (type 8) and Echo Reply (type 0) with source and destination IPs.
- Telnet vs SSH: capture a Telnet login and an SSH login to a router; use Follow TCP Stream to show Telnet's username/password in clear text and SSH's encrypted stream.
- Practical traps: pick ping and login pairs the ACLs allow (e.g. Downtown to Laboratory for ICMP, PC0 for Telnet, PC8/PC9 to Edge for SSH), or capture before the ACLs go on. Windows Firewall blocks ping replies (the Help tab already has the netsh fix). Capture on the PC's Ethernet adapter, not Wi-Fi.

**Scoring (100):** A Initial config, SSH, Telnet 15 · B IPv4 CIDR + VLSM 15 · C DHCPv4 10 · D IPv6 SLAAC + DHCPv6 15 · E EIGRP, RIPv2, static, redistribution 20 · F ACLs 15 · G Wireshark 10

## Possible gaps in the current index.html (verify each one; suggestions only)

1. **Split VTY lines.** The generator writes one `line vty 0 4` or `0 15` block with a single transport mode (see the CLI generator near line 4627). Add a "Telnet on 0 4, SSH on 5 15" mode that writes both blocks, each with its own `transport input` and login method.
2. **Login ACL per VTY range.** The ACL compiler attaches one login ACL per router to one VTY range (`access-class` near lines 3927 and 6798). ACL (a) must go on `line vty 0 4` (Telnet) and ACL (b) on `line vty 5 15` (SSH) of the right routers. Update the ACL explanations, the packet tester and the test plan for this.
3. **Router model 1941.** Only 2911 and 2901 exist (`ROUTER_MODELS`, and the model selects). Add 1941 with g0/0 and g0/1. Update labels that say "Cisco 2901 / 2911".
4. **IPv6 base notation.** The IPv6 field rejects anything but /48 ("Use a /48 prefix (you typed /64)"). Accept the PDF's form `2001:db1:acad::/64` as the base and explain how the /64 subnets are numbered.
5. **G# / S# helper.** Two number inputs that fill the IPv4 base (12G.2S.224.0/20) and IPv6 base (200G:dbS:acad::), and show the working (for scoring B).
6. **"Final Assessment" preset button** next to Sample Lab: builds the routers, models, LANs, WAN links, routing protocols, DHCP-on-Edge relay, and the IPv6 mode for each LAN from the spec above, using the G#/S# values, the exam password, the chosen routing scenario (S1 to S4) and the optional ISP router (see Decisions). Everything it builds stays editable.
7. **Fixed PC addresses.** ACLs (a) and (b) need stable IPs for PC0, PC2, PC4, PC8 and PC9. Add a small PC table (name, LAN, static IP or excluded-from-DHCP IP) that ACL rules can pick from by name, and add those IPs to `ip dhcp excluded-address`.
8. **Assessment checklist tab.** Rubric A to G with points. Each item has the steps, the router to paste on, the verify command(s) (`show ip interface brief`, `show ip eigrp neighbors`, `show ip rip database`, `show ip route`, `show ip dhcp binding`, `show ipv6 dhcp binding`, `show access-lists`, `show running-config | section line vty`, PC `ipconfig /all`), a tick box, and progress per section. Ticks are saved in the project JSON.
9. **Printable addressing table** that matches PDF Section II (Device, IPv4, IPv6).
10. **Wireshark steps.** Nothing about Wireshark exists in the app. Add a step-by-step guide for section IV (see spec above). Where it goes (Help, Troubleshooting, its own tab, or inside checklist item G) is decided with Mal first.

## Topology on its own tab

Today the Topology & CLI tab is three columns: config builder (left), SVG topology (centre, `#svgViewport` / `#topologySvg`), CLI (right). Move the topology drawing to its own main tab; Topology & CLI becomes config builder + CLI with more room.
- Keep the ids so the pan/zoom code keeps working.
- A hidden tab has zero size: re-run the size/"Fit All" logic when the Topology tab is shown, or the drawing comes up empty or tiny.
- Today, clicking a router in the drawing picks it in the CLI pane. Keep that working across tabs (e.g. a "Show CLI" action that switches to the CLI tab with that router selected).

## Redesign

Use the frontend-design and impeccable skills to decide the look (e.g. `/impeccable critique` and `/impeccable audit` first). Screenshot before/after with the Playwright MCP.

- **Not dark mode.** Mal wants it easy on the eyes and easy to read; the skills decide the exact theme, colours and sizes. The current dark theme lives in the `:root` tokens plus some hard-coded colours (see below).
- Who uses it and where: one student, on a laptop beside real routers, switching between this page and PuTTY under time pressure, sometimes on a projector or a printout.
- Readable at arm's length: bigger base text than today's 12-14px, mono font for anything you type into IOS.
- One clear "what do I do next" path through the exam: steps in order, progress visible, Copy buttons large and obvious.
- Fits a 13-inch laptop screen with PuTTY open beside it (half-width window must still work).
- Print styles for the addressing table, scripts and checklist.

Don't break the app:
- The script builds most of the page as HTML strings and relies on existing class names and element ids (`.vlsm-row`, `.acl-rule`, `.cli-chunk`, `#cliTerminal`, `#leftPaneContent` and many more). Keep every id and class name the JS uses; restyle them, or add new classes alongside them.
- Colours are also hard-coded in places outside `:root` (e.g. `#070c17` on the CLI terminal, `#090f1d` on the topology pane, fills and strokes inside the SVG drawing code). Find them all; a light theme with dark patches left over is a bug.
- Still one offline file: no web fonts or CDN. Use system font stacks.
- After the redesign, re-run the full Playwright check (every tab, Sample Lab, assessment preset, Copy in parts, ACL tester) and compare with the before screenshots.

## Decisions (Mal's answers)
- **The exam topology may change on the day.** The preset is a starting point, never hard-coded: adding/removing a router, LAN, PC or link must regenerate addressing, configs, ACLs and the checklist. ACL targets (e.g. "the ISP Router and Edge Router" in ACL b) are picked from a list of the routers that exist.
- **ISP router:** may be the Edge Router, or a separate ISP router may appear. The preset gets an "Add ISP router" option (linked to Edge). ACL (b) lets Mal choose which routers it protects.
- **Passwords:** one "Exam password" input (default `GROUP5CISCO`) that fills enable secret, console, Telnet VTY password and SSH user secret. Each can still be overridden on its own. Also an input for the SSH username.
- **Finance/Accounting IPv6:** any of SLAAC, SLAAC + stateless DHCPv6, or stateful DHCPv6. Every LAN's IPv6 method is a dropdown; the preset uses the PDF's table for R1/R2/R3 and SLAAC for Main.
- **Routing:** the PDF is ambiguous, so the app must support every scenario below (a "Routing scenario" choice in the preset that sets the protocol per link), and show for each one where redistribution happens and why.

## Routing scenarios (the app must generate all of these correctly)

| | R1 to Main | Main to Edge | Edge to R2 | Edge to R3 | Redistribution |
|---|---|---|---|---|---|
| **S1. Edge is the border** (default) | EIGRP | EIGRP | RIPv2 | Static | All on Edge: RIP to EIGRP, EIGRP to RIP, static + connected into both |
| **S2. S1 + ISP router** | EIGRP | EIGRP | RIPv2 | Static | As S1, plus Edge has `ip route 0.0.0.0 0.0.0.0 <ISP>` and sends it on (`redistribute static` in EIGRP, `default-information originate` in RIP). ISP gets one static route back to the whole /20 via Edge |
| **S3. Main is the border** | EIGRP | RIPv2 | RIPv2 | Static | Main: EIGRP to RIP and RIP to EIGRP. Edge: static + connected into RIP |
| **S4. Defaults, minimal redistribution** | EIGRP | Static | RIPv2 | Static | Main: default static to Edge, `redistribute static` into EIGRP so R1 gets the default. Edge: static routes to Registrar/Finance (or one /20 summary) via Main, `default-information originate` in RIP for R2 |

In every scenario:
- R3 has `ip route 0.0.0.0 0.0.0.0 <Edge's IP on the Edge to R3 link>`, and Edge has a static route back to the Laboratories LAN.
- Edge must run RIPv2 on the R2 link. If it doesn't, R2's RIP has no neighbour and R2 can't learn anything, so "Edge static only" is not a valid scenario. Say so in the app if Mal picks it.
- Seed metrics on every redistribute: into EIGRP `metric 1544 2000 255 1 1500` (or a `default-metric`), into RIP `metric 2`. Without them RIP to EIGRP routes don't appear.
- RIP: `version 2`, `no auto-summary`.
- **Class A trap:** with the PDF's base `12G.2S.224.0`, the first octet is 121 to 125, which is class A. RIP's classful `network 121.0.0.0` turns RIP on for EVERY 121.x interface on that router. Use `passive-interface` on links that must not speak RIP. Write EIGRP network statements with wildcards (`network <link> 0.0.0.3`) so EIGRP runs only on the intended links. Check the app does this.
- S4 caveat: if Edge uses a default to Main AND Main a default to Edge, packets to an unknown network bounce between them until the TTL runs out. Prefer specific routes (or the /20 summary) on Edge. Note it in the app.
- IPv6 mirrors IPv4 (RIPng / EIGRP for IPv6 / `ipv6 route ::/0`), or static IPv6 routes everywhere. It's only needed for IPv6 pings across LANs.
- The Verify list for each scenario: R1 `show ip route` shows D EX routes for Downtown and Laboratories; R2 shows R routes for the EIGRP side; R3 shows `S* 0.0.0.0/0`; on the border router, `show ip protocols` lists the redistribution.
- **Edge default route:** an option, on by default. Edge gets `ip route 0.0.0.0 0.0.0.0` to the ISP if one exists, otherwise to Null0, and passes it into EIGRP/RIP. The PDF lists Edge under "Default Static Route", so a grader may look for `S* 0.0.0.0/0` on Edge.
- **ISP link interface:** Edge's three Gigabit ports are used by Main, R2 and R3. The ISP link's interface is selectable; default `s0/0/0` (HWIC-2T, clock rate on the DCE end). Mal checks the real hardware on the day.

## ACL and access decisions
- **Return traffic:** ACL (c) denies Lab to Registrar but permits `tcp ... established` and `icmp ... echo-reply` from Lab to Registrar above the deny, so Registrar can initiate to Lab. Every other deny in (c) and (d) gets the same per-rule option ("allow replies to connections the other side starts"), default on, so a deny only stops the side that starts.
- **Telnet vs (c)/(d):** above the denies, permit `tcp host <PC> host <gateway> eq 23` from PC2 and PC4 to the router gateway IPs that sit inside LANs they're denied (R1's Registrar gateway, Main's Finance gateway). Host to host only, nothing wider. The Telnet test steps also note that WAN IPs always work.
- **Telnet login on vty 0 4:** line password + `login` by default (the PDF says "passwords GROUP5CISCO"); username + `login local` as a dropdown option. SSH on vty 5 15 always uses `login local`.
- **Stateful DHCPv6 (R3):** `ipv6 nd prefix <prefix>/64 no-autoconfig` so PCs only get the DHCPv6 address. A checkbox, default on.
