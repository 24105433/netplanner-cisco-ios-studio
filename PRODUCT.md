# Product

<!-- impeccable:product-schema 1 -->

<!-- Written from CLAUDE.md (Mal's notes from an earlier chat) without a separate interview: Mal asked the designer to decide. Facts below come from CLAUDE.md and the assessment PDF. -->

## Platform

web

## Users

One student (Mal), CPE 3105 Network and Security, during the Final Skills-Based Laboratory Assessment. Mal sits at a lab PC or a 13-inch laptop beside real Cisco 1941/2911 routers and 2960 switches, switching between this page and PuTTY (console at 9600 baud) under time pressure. The page is sometimes shown on a projector or printed and kept beside the routers.

## Product Purpose

NetPlanner & Cisco IOS Studio turns the exam's addressing block and topology into everything needed on the day: VLSM subnets (IPv4 and IPv6), a drawing of the topology, a ready-to-paste IOS script per router (init config, interfaces, DHCP, routing, ACLs, verify commands), ACLs explained line by line with a packet tester, and help/troubleshooting pages. Success: Mal finishes every graded section (A to G, 100 points) correctly and on time, without typing IOS from memory.

## Positioning

Built for one physical exam, not a simulator: it writes scripts in small paste-safe parts for a slow console, says which prompt each part goes at, and explains why each line is there.

## Operating Context

- Real hardware, PuTTY console, copy then right-click to paste, wait for the prompt.
- Half-width browser window next to PuTTY must still work.
- Read at arm's length; sometimes on a projector or a printout.
- Offline lab PC: no internet.

## Capabilities and Constraints

- One HTML file, no CDN, no web fonts, no external libraries. System font stacks only.
- Old saved project .json files must keep loading.
- The JavaScript builds most of the page as HTML strings and depends on existing ids and class names; they must not change.
- Not dark mode (Mal's request): easy on the eyes and easy to read.

## Product Principles

- Next step obvious: the exam order is the path through the app.
- Anything typed into IOS is shown in a monospace face and is one click to copy.
- Readability over density: big enough to read beside a router.
- Never lose work: save/load project, nothing hidden behind a reload.

## Accessibility & Inclusion

Body text larger than the old 12-14px; strong contrast (WCAG AA at least) because the room lighting and projectors vary.
