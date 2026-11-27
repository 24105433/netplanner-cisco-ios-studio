# Design: NetPlanner "cable label" light theme

Replaces the old dark slate/indigo theme (Nov 2026). Everything lives in the `:root` tokens at the top of `index.html`.

## World

TIA-606, the cable and port labelling standard used on real racks: black print on white label stock, and one fixed colour per role. The app borrows only type, palette and one signature move from it; layout and controls stay standard web controls.

**Signature move:** role colour. The same colour always means the same thing, in the forms, the subnet table, the CLI and the drawing:

| Role | Token | Hex |
|---|---|---|
| LAN (station cabling) | `--cyan` / `--cyan-ink` / `--cyan-soft` | #0a5fae / #08467f / #e4effa |
| WAN link | `--amber` / `--amber-ink` / `--amber-soft` | #a84700 / #7a3300 / #fcecdc |
| Router, selection, the main action (Copy) | `--accent` / `--accent-ink` / `--accent-soft` | #5b2d96 / #4a2380 / #efe8f8 |
| Works / permit | `--emerald` … | #11703a / #0b5229 / #e1f2e7 |
| Fault / deny | `--rose` … | #b8143a / #8e0f2c / #fbe5ea |

Ground `#e8ecf1`, cards white, text `#14181f` / `#424b58` / `#5c6573`. All text pairs pass WCAG AA (≥4.5:1). Routing protocols in the drawing: RIP green, OSPF purple, EIGRP orange-red, static grey (`PROTO_COLOR`).

## Type

System fonts only (the file must work offline):
- Labels, tabs, headings: `--font-label` (Bahnschrift on Windows, DIN Alternate on macOS): signage lettering.
- Body: `--font-sans` (Segoe UI / system UI), 16px base.
- Anything typed into IOS: `--font-mono` (Cascadia Mono / Consolas / Menlo), 15px in the CLI.
- Labels are sentence case, never ALL CAPS.

## Rules

- No dark patches: the CLI is printed paper (`--bg-code`), the drawing ground is `#f6f8fb`.
- No side stripes on rounded boxes: role and verdict show as a tint plus a full border (VLSM rows, ACL lines, test steps).
- Badges, port chips and tabs are square-cornered labels (3–4px radius); cards 12px.
- One button per job is violet: the part Copy button is the largest control in the CLI pane; once used it turns green with "Copied ✓".
- Keep every id and class the JS uses; restyle, don't rename.
- Print styles hide the chrome and print the open tab (addressing table, help, or the script parts).
