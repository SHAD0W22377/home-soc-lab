# Home SOC Lab — VirtualBox + Kali Linux + Splunk

## Overview
Built a home Security Operations Center (SOC) lab to simulate 
real-world attacks and practice detection engineering using 
free/open-source tools.

## Lab Architecture
- **Attacker:** Kali Linux VM (192.168.56.102)
- **Target:** Windows 11 VM (192.168.56.101)
- **SIEM:** Splunk Enterprise (Free tier, 500MB/day)
- **Network:** VirtualBox Host-Only Adapter (isolated)

## Tools Used
| Tool | Purpose |
|------|---------|
| VirtualBox | Hypervisor |
| Kali Linux | Attacker machine |
| Nmap | Port scanning / reconnaissance |
| Hydra | Credential brute forcing |
| Splunk Enterprise | SIEM and log analysis |
| Splunk Universal Forwarder | Log collection agent |

## Attack Simulations

### 1. Port Scan (Nmap)
Simulated reconnaissance against the Windows target.
```bash
nmap -sT -p 1-1000 192.168.56.101
```
**MITRE ATT&CK:** T1046 — Network Service Discovery

### 2. Brute Force (Hydra + PowerShell simulation)
Simulated credential attack generating failed login events.
```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt 
smb://192.168.56.101 -t 1
```
**MITRE ATT&CK:** T1110 — Brute Force

## Splunk Detections

### Port Scan Detection
Flags any source IP hitting more than 3 unique ports.
```spl
index=main sourcetype="WinEventLog:Security" EventCode=5156
| stats dc(Destination_Port) as ports_hit by Source_Address
| where ports_hit > 3
| sort -ports_hit
```

### Brute Force Detection
Flags any account with more than 5 failed login attempts.
```spl
index=main sourcetype="WinEventLog:Security" EventCode=4625
| stats count by Account_Name ComputerName
| where count > 5
| sort -count
```

## Windows Event Codes Reference
| Event Code | Description |
|------------|-------------|
| 4625 | Failed logon attempt |
| 4624 | Successful logon |
| 5156 | Firewall allowed connection |
| 5157 | Firewall blocked connection |

## Screenshots

### Brute Force Detection
![Brute Force](screenshots/Login-Attempts-Timestamps.png)

### Port Scan Detection
![Port Scan](screenshots/Port-Scan-Timeline.png)

### Splunk Alerts
![Alerts](screenshots/Alerts.png)

## Key Takeaways
- Configured end-to-end log pipeline from Windows to Splunk
- Wrote SPL detection queries for real attack patterns
- Mapped detections to MITRE ATT&CK framework
- Practiced real attacker tooling in an isolated environment
- Documented findings for professional portfolio
