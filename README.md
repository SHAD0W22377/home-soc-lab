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
- VirtualBox — hypervisor
- Kali Linux — attacker machine
- Nmap — port scanning / reconnaissance
- Hydra — credential brute forcing
- Splunk Enterprise — SIEM and log analysis
- Splunk Universal Forwarder — log collection

## Attack Simulations

### 1. Port Scan (Nmap)
Simulated reconnaissance against the Windows target.
```bash
nmap -sT -p 1-1000 192.168.56.101
```
**MITRE ATT&CK:** T1046 — Network Service Discovery

### 2. Brute Force (Hydra)
Simulated credential attack against SMB.
```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt 
smb://192.168.56.101 -t 1
```
**MITRE ATT&CK:** T1110 — Brute Force

## Splunk Detections

### Port Scan Detection
```spl
index=main sourcetype="WinEventLog:Security" EventCode=5156
| stats dc(Destination_Port) as ports_hit by Source_Address
| where ports_hit > 3
| sort -ports_hit
```

### Brute Force Detection
```spl
index=main sourcetype="WinEventLog:Security" EventCode=4625
| stats count by Source_Address Account_Name
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
![Dashboard](screenshots/splunk-dashboard.png)
![Port Scan Alert](screenshots/port-scan-alert.png)
![Failed Logins](screenshots/failed-login-alert.png)

## Key Takeaways
- Configured end-to-end log pipeline from Windows to Splunk
- Wrote SPL detection queries for real attack patterns
- Mapped detections to MITRE ATT&CK framework
- Practiced real attacker tooling in an isolated environment
