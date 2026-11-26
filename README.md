# Network Reconnaissance Lab (Nmap)

**Overview:** This report includes educational port scanning performed strictly on the authorized public test target `scanme.nmap.org` (`45.33.32.156`) using a controlled Kali Linux lab environment. Raw scan output is saved in [`scan_results.txt`](./scan_results.txt).

## 1. Nmap Scanning Command & Methodology
```bash
sudo nmap -sS -sV -oN scan_results.txt scanme.nmap.org
```
<img src=./nmap_scan_result.png alt="Nmap scanning output & saving report in scan_results.txt">

**Why use SYN Scan `-sS` instead of TCP Connect `-sT`:** A SYN scan sends a `SYN` packet, waits for `SYN-ACK` to confirm the port is open, and immediately sends `RST` without completing the 3-way handshake. Because a full TCP connection is never established, `-sS` is faster and leaves fewer traces in application-level logs compared to `-sT`.

## 2. Discovered Ports & Services (`scanme.nmap.org` / `45.33.32.156`)
| Port | State | Service | Version / Notes |
| :--- | :--- | :--- | :--- |
| `22/tcp` | open | ssh | OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13 (remote Linux admin access; outdated version) |
| `53/tcp` | filtered | domain | DNS service (packets filtered by firewall) |
| `80/tcp` | open | http | Apache httpd 2.4.7 (Ubuntu) (unencrypted web traffic; outdated version) |
| `9929/tcp` | open | nping-echo | Nping echo (Nmap diagnostic test service) |

*Additional findings:* OS detected as Linux (`cpe:/o:linux:linux_kernel`); 996 TCP ports closed (reset); host is up (2.2s latency). 

## 3. High-Risk Ports Assessment (445/SMB & 3389/RDP)
* **Port 445 (SMB):** Exposes Windows shared folders and file services. Leaving SMB open to the internet creates critical risks of unauthorized data access, credential relay attacks, and ransomware/worm propagation.
* **Port 3389 (RDP):** Provides Remote Desktop access to Windows hosts. When exposed publicly without a VPN, it becomes a primary target for brute-force and password-spraying attacks.

## 4. Remediation Recommendations
 1. **Block external exposure:** Restrict ports `445` and `3389` at the perimeter firewall so they are unreachable from the public internet.
 2. **Enforce VPN & allowlisting:** Allow administrative access (`SSH`/`RDP`/`SMB`) only through a corporate VPN or from trusted IP addresses.
 3. **Harden authentication & protocols:** Disable legacy SMBv1, require NLA for RDP, enforce strong passwords or SSH keys, and enable Multi-Factor Authentication (MFA).
