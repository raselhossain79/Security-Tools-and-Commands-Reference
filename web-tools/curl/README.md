# Pentest Arsenal

A categorized, practical reference of 120+ penetration testing and security tools, organized by where each one fits in a real engagement — from initial recon to final reporting.

This is not just a list of names. Each entry notes **what the tool does** and **where it belongs in the workflow**, so you can find the right tool fast instead of searching through bookmarks and old notes.

> For educational use and authorized security testing only. Always get written permission before scanning, testing, or assessing any system or network you do not own.

---

## Table of Contents

- [How to Use This Repo](#how-to-use-this-repo)
- [Engagement Workflow](#engagement-workflow)
- [01. Reconnaissance & Information Gathering](#01-reconnaissance--information-gathering)
- [02. DNS, Network & Traffic Analysis](#02-dns-network--traffic-analysis)
- [03. Web Application Testing](#03-web-application-testing)
- [04. Vulnerability Assessment](#04-vulnerability-assessment)
- [05. Password Auditing & Authentication](#05-password-auditing--authentication)
- [06. Exploitation & Security Frameworks](#06-exploitation--security-frameworks)
- [07. Active Directory, Post-Exploitation & Pivoting](#07-active-directory-post-exploitation--pivoting)
- [08. Wireless & Network Security](#08-wireless--network-security)
- [09. Cloud, API & Mobile Security](#09-cloud-api--mobile-security)
- [10. Digital Forensics & Incident Response](#10-digital-forensics--incident-response)
- [11. Reverse Engineering & Malware Analysis](#11-reverse-engineering--malware-analysis)
- [12. Reporting & Documentation](#12-reporting--documentation)
- [Installation Notes](#installation-notes)
- [Contributing](#contributing)
- [Disclaimer](#disclaimer)

---

## How to Use This Repo

- Use the table of contents to jump straight to the phase you're working in.
- Each table lists the tool, a one-line description, and typical use case.
- Star/bookmark this repo as a personal lookup — the goal is speed during an engagement, not a tutorial.
- Pair this with your own `Recon-OSINT` and web/API vulnerability note libraries for the full picture: this repo answers "which tool," those answer "how does the vulnerability work."

## Engagement Workflow

A typical order of operations this list is structured around:

1. **Scope & authorization** — confirm written permission and boundaries before anything else.
2. **Reconnaissance** — passive and active information gathering, attack surface mapping.
3. **Enumeration & vulnerability assessment** — identify live services, versions, and known weaknesses.
4. **Manual validation** — confirm findings by hand; never trust automated scanners blindly.
5. **Exploitation** — within scope only, using the minimum necessary impact.
6. **Post-exploitation & pivoting** — privilege escalation, lateral movement, persistence checks (as authorized).
7. **Documentation & reporting** — every finding, every step, written clearly for the client.

---

## 01. Reconnaissance & Information Gathering

| Tool | Description |
|---|---|
| Nmap | Scans networks, hosts and open ports. |
| Masscan | Performs high-speed port scanning. |
| Netdiscover | Finds devices on local networks. |
| theHarvester | Collects public emails, domains and hostnames. |
| Recon-ng | Automates web-based reconnaissance. |
| Maltego | Maps relationships between people, domains and organizations. |
| Amass | Discovers subdomains and maps attack surfaces. |
| Subfinder | Finds subdomains using passive sources. |
| WhatWeb | Identifies website technologies. |
| Wappalyzer | Detects web technologies and frameworks. |
| Fierce | Helps discover DNS-related hosts and subdomains. |
| Dmitry | Collects basic domain and host information. |
| Naabu | Fast port scanner built for large attack surfaces. |
| httpx | Probes hosts to find live web services and their details. |

## 02. DNS, Network & Traffic Analysis

| Tool | Description |
|---|---|
| Wireshark | Captures and analyzes network packets. |
| tcpdump | Captures network traffic from the command line. |
| Tshark | Analyzes packets through a command-line interface. |
| Netcat | Reads and sends data over network connections. |
| Socat | Connects data streams between endpoints. |
| dig | Queries DNS records. |
| nslookup | Checks DNS information for domains. |
| DNSrecon | Performs DNS reconnaissance. |
| DNSenum | Collects DNS and subdomain information. |
| traceroute | Shows the network path to a destination. |
| MTR | Combines route tracing and network diagnostics. |
| dnsx | Runs fast, bulk DNS queries and resolves large host lists. |

## 03. Web Application Testing

| Tool | Description |
|---|---|
| Burp Suite | Tests web requests and application security. |
| OWASP ZAP | Scans web applications for security issues. |
| Nikto | Checks web servers for common weaknesses. |
| Gobuster | Finds web directories, files and DNS entries. |
| Feroxbuster | Discovers web content through recursive scanning. |
| Dirsearch | Searches for hidden web paths and files. |
| ffuf | Fuzzes web applications to find unexpected behavior. |
| WPScan | Assesses WordPress security. |
| Skipfish | Performs automated web application reconnaissance. |
| Wfuzz | Tests web applications using configurable inputs. |
| SSLScan | Checks SSL/TLS configurations and supported protocols. |
| Katana | Crawls web applications to map endpoints and JavaScript files. |
| Arjun | Discovers hidden HTTP parameters. |
| Dalfox | Tests web applications for XSS and parameter issues. |

## 04. Vulnerability Assessment

| Tool | Description |
|---|---|
| Greenbone / OpenVAS | Scans systems for known vulnerabilities. |
| Nessus | Assesses hosts for security vulnerabilities. |
| Nuclei | Detects known issues using security templates. |
| Searchsploit | Searches a local database of exploit references. |
| Lynis | Audits Linux systems for security weaknesses. |
| Linux Exploit Suggester | Identifies possible local privilege-escalation issues. |
| enum4linux-ng | Enumerates information from Windows/Samba services. |
| smbclient | Connects to and inspects SMB shares. |
| rpcclient | Queries information from RPC services. |
| testssl.sh | Checks TLS security and configuration. |
| OpenSCAP | Evaluates systems against security compliance rules. |

## 05. Password Auditing & Authentication

| Tool | Description |
|---|---|
| John the Ripper | Audits password hashes for weak passwords. |
| Hashcat | Performs password recovery and hash auditing. |
| Hydra | Tests authentication strength across supported services. |
| Medusa | Performs modular login security testing. |
| Ncrack | Audits network service authentication. |
| Patator | Runs flexible multi-protocol login and brute-force testing. |
| CeWL | Creates wordlists from website content. |
| Crunch | Generates custom wordlists. |
| CUPP | Creates personalized password-audit wordlists. |
| Hashid | Suggests possible hash types. |
| Ophcrack | Recovers certain Windows passwords using rainbow tables. |
| Kerbrute | Enumerates Active Directory users and tests passwords over Kerberos. |

## 06. Exploitation & Security Frameworks

| Tool | Description |
|---|---|
| Metasploit Framework | Tests known vulnerabilities in controlled environments. |
| msfvenom | Generates payloads for authorized security testing. |
| ExploitDB | Provides a searchable collection of exploit references. |
| BeEF | Assesses browser security in controlled tests. |
| SQLmap | Tests for SQL injection vulnerabilities. |
| Commix | Checks for command injection vulnerabilities. |
| Impacket | Provides tools and libraries for network protocol testing. |
| Responder | Analyzes certain name-resolution and authentication exposures. |
| NetExec | Assesses network services in authorized environments (active successor to CrackMapExec). |
| BloodHound | Maps Active Directory relationships and attack paths. |
| CrackMapExec | Legacy network assessment framework. The original project is no longer maintained and NetExec is its active successor, but it is still usable in many labs and environments. |

## 07. Active Directory, Post-Exploitation & Pivoting

| Tool | Description |
|---|---|
| Evil-WinRM | Provides a remote shell over WinRM for authorized Windows testing. |
| Certipy | Finds and tests Active Directory Certificate Services misconfigurations. |
| Rubeus | Tests Kerberos ticket handling and abuse paths in AD. |
| Mimikatz | Demonstrates how Windows credentials can be extracted and abused. |
| Chisel | Creates fast TCP/UDP tunnels over HTTP for pivoting. |
| Ligolo-ng | Builds tunnels into internal networks without SOCKS proxies. |
| Sliver | Open-source command-and-control framework for red team operations. |
| LinPEAS | Enumerates Linux privilege-escalation paths. |
| WinPEAS | Enumerates Windows privilege-escalation paths. |
| pspy | Monitors Linux processes without root access. |

## 08. Wireless & Network Security

| Tool | Description |
|---|---|
| Aircrack-ng | Audits Wi-Fi security and captures wireless traffic. |
| Airodump-ng | Monitors nearby wireless networks and packets. |
| Aireplay-ng | Generates wireless traffic for authorized testing. |
| Kismet | Detects and monitors wireless networks. |
| Wifite | Automates supported wireless security checks. |
| Reaver | Tests WPS security on compatible wireless devices. |
| Bully | Audits WPS security on supported networks. |
| Bettercap | Performs network reconnaissance and security testing. |
| Ettercap | Analyzes network traffic and tests interception risks. |
| Yersinia | Tests weaknesses in selected network protocols. |
| hping3 | Generates custom network packets for diagnostics and testing. |

## 09. Cloud, API & Mobile Security

| Tool | Description |
|---|---|
| Postman | Builds, sends and organizes API requests for manual testing. |
| Prowler | Audits AWS, Azure and GCP against security best practices. |
| ScoutSuite | Reviews multi-cloud security posture. |
| Pacu | Tests AWS environments for privilege-escalation paths. |
| Trivy | Scans containers, code and IaC for vulnerabilities and misconfigurations. |
| MobSF | Performs automated static and dynamic mobile app analysis. |
| Frida | Instruments running apps to inspect and modify behavior at runtime. |
| Objection | Explores mobile app runtime security using Frida. |

## 10. Digital Forensics & Incident Response

| Tool | Description |
|---|---|
| Autopsy | Investigates digital evidence through a forensic interface. |
| The Sleuth Kit | Analyzes disk images and file systems. |
| Volatility | Examines memory images for forensic evidence. |
| Foremost | Recovers files through file carving. |
| Scalpel | Recovers files from disk images and raw data. |
| Binwalk | Analyzes firmware and embedded files. |
| ExifTool | Reads and edits file metadata. |
| YARA | Identifies files using pattern-based rules. |
| ClamAV | Scans files for known malware signatures. |
| Plaso | Builds forensic timelines from digital artifacts. |
| Guymager | Creates forensic disk images. |

## 11. Reverse Engineering & Malware Analysis

| Tool | Description |
|---|---|
| Ghidra | Disassembles and decompiles software binaries. |
| Radare2 | Analyzes and reverse-engineers executable files. |
| GDB | Debugs programs to inspect their behavior. |
| strace | Tracks system calls made by Linux programs. |
| ltrace | Tracks library calls made by programs. |
| JADX | Decompiles Android application packages. |
| APKTool | Decodes and rebuilds Android application resources. |
| PE-bear | Inspects Windows PE executable structures. |
| CAPA | Detects capabilities in executable files. |

## 12. Reporting & Documentation

| Tool | Description |
|---|---|
| Dradis | Organizes penetration-testing findings and reports. |
| Faraday | Centralizes scan results and findings across a team. |
| PwnDoc | Generates pentest reports from reusable finding templates. |
| Ghostwriter | Manages engagements, findings and report generation. |

---

## Installation Notes

Most tools here fall into one of these install patterns:

- **Pre-installed on Kali/Parrot**: Nmap, Wireshark, tcpdump, Hydra, John the Ripper, Hashcat, Metasploit, Aircrack-ng suite, Burp Suite (Community), Nikto, and most of the forensics/RE tools.
- **Go-based (`go install ...@latest`)**: Amass, Subfinder, Naabu, httpx, dnsx, Katana, Nuclei, ffuf, Dalfox.
- **Python/pip (`pip install` or `pipx install`)**: theHarvester, CUPP, Pacu, Prowler, ScoutSuite, Objection, Impacket.
- **Go/Rust/standalone binaries (GitHub releases)**: NetExec, BloodHound, Certipy, Rubeus, Ligolo-ng, Chisel, Sliver, Trivy.
- **Manual/GitHub clone**: Responder, LinPEAS/WinPEAS, Mimikatz, Dradis, Faraday, Ghostwriter, PwnDoc.

When in doubt, check the tool's own GitHub repo for the current recommended install method — several of these (NetExec, Nuclei, Katana) update their install instructions often.

## Contributing

This is primarily a personal reference, but suggestions are welcome:

1. Open an issue or PR with the tool name, one-line description, and which category it belongs in.
2. Keep descriptions factual and neutral — no marketing language.
3. Avoid duplicate entries; check existing categories first.

## Disclaimer

All tools listed here are intended for **authorized security testing, research, and educational purposes only**. Always obtain explicit written permission before scanning, testing, or assessing any system or network you do not own. Unauthorized access or testing may violate applicable laws.
