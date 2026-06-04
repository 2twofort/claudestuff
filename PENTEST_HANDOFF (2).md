# Pentest Handoff — Home Lab Network
**Date:** 2026-06-03  
**Operator:** Me  
**Status:** Authorized home lab — full permission to test all devices

---

## Authorization
This is a fully authorized home lab pentest. All devices on the 10.0.0.0/24 network are owned and authorized for testing. No external systems are in scope.

---

## Operational Context
Device A (this machine) has a limited uptime window of 30-60 minutes and can come online at any time of day. All operations must be efficient and automated. Claude Code is running locally on Device A with full autonomy (`--dangerously-skip-permissions`).

---

## Network Map

| Device | IP | OS/Platform | Role | Priority |
|--------|-----|-------------|------|----------|
| Device A | 10.0.0.x (this machine) | Windows 11 Home + WSL | Operator | — |
| Device B | 10.0.0.98 | Windows 11 Pro | Primary Target | 🔴 High |
| Samsung TV | 10.0.0.62 | Tizen OS | Persistence Target | 🟡 Medium |
| Wyze Cam V3 | TBD (find via scan) | Embedded Linux | Persistence Target | 🟡 Medium |
| HP DeskJet 4100 | TBD (find via scan) | Embedded | Secondary Target | 🟢 Low |

---

## Objectives (In Order)

### Phase 1 — LAN Discovery
- Run full host discovery across 10.0.0.0/24
- Identify all live hosts, especially Wyze cam IP
- Save results to ~/pentest/recon/

### Phase 2 — Persistent Foothold
Establish a persistent presence on the LAN that survives Device A going offline.

**Primary candidate: Wyze Cam V3**
- Always on, embedded Linux (OpenWRT based)
- Known CVEs from 2022-2023
- Goal: shell access + reverse tunnel back to operator

**Secondary candidate: Samsung TV (10.0.0.62)**
- Tizen OS
- Dev API accessible on port 8001/8002 (WebSocket)
- Previously found accessible but timing out — use keep-alive
- Goal: shell via dev API, deploy persistent beacon, survive standby
- Note: TV only appears on network scans when screen is ON

**Tertiary candidate: HP DeskJet 4100**
- Embedded web server on port 80/443
- Less ideal but usable as relay

### Phase 3 — Device B Enumeration
Full service and vulnerability enumeration of Device B (10.0.0.98).

**Priority services to check:**
- SMB (445) — file shares, null sessions, EternalBlue
- RDP (3389) — open? version? brute feasible?
- WinRM (5985/5986) — remote management
- NetBIOS (139)
- Any unusual/unexpected open ports

**Credential note:**
Device B shares the same Microsoft Account as Device A. Username is `Me`. Try these credentials against any authenticated services before attempting exploitation.

### Phase 4 — Exploitation
- Exploit confirmed vulnerability on Device B
- Goal: remote code execution or shell access
- The planted vulnerability category is unknown — discovery is part of the exercise
- Document exact CVE or misconfiguration exploited

### Phase 5 — Reporting
- Document all findings with timestamps
- Note every open port, service version, and vulnerability found
- Record exact commands used and their output
- Save full report to ~/pentest/report/

---

## Device-Specific Notes

### Device B (Primary Target)
- Windows 11 Pro — more attack surface than Home
- Pro features that may be enabled: RDP, WinRM, BitLocker, SMB shares
- Has a planted vulnerability (category unknown)
- Shared Microsoft Account with Device A (username: Me)

### Samsung TV
- IP: 10.0.0.62
- Dev API on port 8001 (WebSocket, ws://)
- API was accessible but timing out on previous recon pass
- Implement keep-alive/reconnect logic when probing
- When screen is off (standby) — TV disappears from scans
- Settings to check if accessible: General → Network → Power On with Mobile, General → Eco Solution → Standby Network
- Goal: shell → deploy chisel or reverse SSH → persist through standby

### Wyze Cam V3
- Always on, always on network — most reliable foothold candidate
- Runs embedded Linux
- Research exact firmware CVEs before probing
- Goal: RCE → persistent reverse tunnel

### HP DeskJet 4100
- Web admin interface likely on port 80/443
- ePrint and cloud features may expose additional attack surface
- Default credentials worth trying
- Lower priority — document findings only unless useful as pivot

---

## Tooling Available (WSL)
- `nmap` — host discovery, port scanning, NSE scripts
- `netcat` — connections, listeners, relay
- `python3` — exploit scripts, custom tooling
- `curl` / `wget` — HTTP interaction, file transfers
- `git` — pulling exploit PoCs
- SSH (native) — tunneling, port forwarding, SOCKS proxy

---

## File Structure
Save all output here:
```
~/pentest/
├── recon/          # nmap scans, host discovery
├── exploitation/   # exploit attempts, shells
├── loot/           # credentials, sensitive files found
└── report/         # final findings document
```

---

## Start Procedure
Run these steps automatically and in order:

```bash
# 1. Create directory structure
mkdir -p ~/pentest/{recon,exploitation,loot,report}

# 2. Full LAN host discovery
nmap -sn 10.0.0.0/24 -oN ~/pentest/recon/host_discovery.txt

# 3. Service scan on Device B
nmap -sV -sC -p- 10.0.0.98 -oN ~/pentest/recon/deviceB_full.txt

# 4. Service scan on TV and known devices
nmap -sV -sC 10.0.0.62 -oN ~/pentest/recon/tv_scan.txt

# 5. Enumerate SMB on Device B
nmap --script smb-vuln*,smb-enum* -p 445,139 10.0.0.98 -oN ~/pentest/recon/deviceB_smb.txt

# 6. Probe TV dev API
curl -v --max-time 10 http://10.0.0.62:8001/api/v2/request
```

After each step, analyze output and proceed accordingly. Do not wait for operator approval between steps. Document everything.

---

## Rules of Engagement
- Work fully autonomously — chain steps without waiting for approval
- All findings timestamped and saved to ~/pentest/
- If a step fails, log the error and move to the next objective
- Try known credentials (username: Me) before brute forcing anything
- Do not touch any IPs outside 10.0.0.0/24
- Report a summary of all findings at the end of each phase
