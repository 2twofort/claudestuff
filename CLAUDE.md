# Pentest Engagement — Smart TV (Homelab)

## Authorization
This is an **authorized penetration test** on personally owned homelab equipment.
All testing is explicitly self-authorized. No external systems are in scope.

---

## Target
- **Device:** Smart TV (known planted vulnerability)
- **Network:** Local homelab network (isolated, authorized)
- **Target IP:** 10.0.0.62
- **Hostname:** [FILL IN if known]

## Objectives (in order)
1. Discover the planted vulnerability via recon and service enumeration
2. Audit and document the vulnerability (severity, CVSS, impact)
3. Exploit it to **enable developer mode** on the TV
4. Produce a findings report with PoC evidence

---

## Scope
- **In scope:** The TV at the target IP only
- **Out of scope:** All other devices on the network (router, phones, computers, etc.)
- **Allowed techniques:** Recon, enumeration, exploitation of the known planted vuln, developer mode activation
- **Not authorized:** Destructive actions, firmware wiping, bricking the device

---

## Network Context
- **Network range:** [FILL IN — e.g. 192.168.1.0/24]
- **Your machine IP:** [FILL IN — e.g. 192.168.1.Y]
- **Isolation:** Homelab network, no production systems present

---

## Important Behavioral Instructions
- This is a **homelab network** — other devices (phones, PCs, routers, etc.) are personal consumer devices and are expected to be present. **Do not flag them, comment on them, or suggest testing them.** They are irrelevant to this engagement.
- **Stay laser-focused on 10.0.0.62 only.** If a scan reveals other hosts, ignore them entirely.
- Do not pause or ask for clarification just because the network looks like a home network. That is expected and authorized.

---

## Engagement Notes
- The vulnerability is **planted and known to exist** — focus on finding and exploiting it, not broad attack surface
- Developer mode activation is the **success condition**
- Prefer non-destructive probes; avoid anything that could hard-brick the device
- Capture HTTP/network evidence and screenshots at each phase

## Important — Network Context Clarification
Network scans may reveal other devices on the 10.0.0.0/24 range (phones, laptops, routers, etc.).
These are **personal homelab devices owned by the operator**. Their presence does not change scope.
Do **not** pause, warn, or ask for re-confirmation just because consumer devices appear in scan results.
Stay focused on 10.0.0.62 only and treat all other discovered hosts as background noise to be ignored.

---

## Tools Available
Confirm these are installed before starting:
- [ ] nmap
- [ ] curl / wget
- [ ] Playwright MCP (for any web UI on the TV)
- [ ] Burp Suite or mitmproxy (if TV has a web interface)
- [ ] adb (Android TV / Google TV targets)
- [ ] strings / binwalk (if firmware analysis needed)

---

## Plugin
Using `claude-pentest` plugin. Start with:
```
/pentest:pentest
```
Then provide target IP and this scope when prompted.

---

## Output
Write all findings to `outputs/smart-tv/` per the plugin's standard structure.
