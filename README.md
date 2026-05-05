Advanced Ethical Hacking
Platform: MITRE CALDERA
Environment: Isolated VirtualBox lab (Red Hat Linux, Windows 10, Kali Linux)

Overview
This lab simulates real-world attacker behaviour using MITRE CALDERA, an open-source adversary emulation platform built on the ATT&CK framework. Rather than waiting for a real attack, CALDERA allows you to safely execute attacker TTPs (Tactics, Techniques, and Procedures) against live endpoints in a controlled environment.
The core question this lab answers: are the defenses working, and where are the blind spots?

Lab Environment
The C2 server ran MITRE CALDERA on Red Hat Linux. Sandcat agents were deployed to two targets — a Windows 10 VM and a Kali Linux VM — within an isolated host-only VirtualBox lab environment.

Setup and Deployment
CALDERA Server

Cloned CALDERA from MITRE GitHub using git clone --recursive
Installed Python dependencies via pip install -r requirements.txt
Built and started the server with python3 server.py --build
Accessed the web interface and authenticated as Red Team operator

Agent Deployment — Windows Target
Deployment exposed layered endpoint defenses in sequence:

Windows Defender AV detected and quarantined the Sandcat payload immediately on first execution — confirming real-time protection was active
Windows Defender Firewall blocked the outbound C2 callback on second attempt — confirming network controls were active
After disabling both controls (lab configuration to simulate a compromised or misconfigured host), the agent connected successfully with elevated Administrator privileges

Agent Deployment — Kali Linux Target
The bash agent deployed without interference. Both agents confirmed live and communicating in the CALDERA Agents dashboard.

Operations Executed
Operation 1 — Discovery Test
Profile: Discovery (pre-built CALDERA ATT&CK profile)
Target: Windows VM
Identify active users — Successful. Returned currently logged-in user accounts.
Identify local users — Successful. Enumerated all local accounts on the machine.
Find user processes — Successful. Mapped running processes to user accounts.
View admin shares — Successful. Enumerated hidden ADMIN$ share — data returned and confirmed exfiltrated.
Discover domain controller — Failed. Expected result — the VM is not domain-joined.
Finding: The View admin shares TTP returned structured data detailing the ADMIN$ share properties, confirming successful execution and live data collection from the endpoint.

Operation 2 — Enumerator Test
Profile: Enumerator
Target: Windows VM
WMIC Process Enumeration — Successful. Full process list collected and executed across multiple agents.
tasklist Enumeration — Successful. Returned executable paths and process names.
UAC Status Check — Successful. Confirmed UAC configuration on the target machine.
Finding: WMIC-based enumeration returned detailed process intelligence including executable paths, confirming full visibility into the running state of the compromised endpoint. This level of access would allow an attacker to identify security tools, plan lateral movement, and select next-stage techniques.

Blue Team Observations
The simulation identified the following detection opportunities that a SOC or SIEM should be configured to catch:

Obfuscated or encoded PowerShell execution at the command line
Outbound connections from endpoints to non-standard ports
WMIC process enumeration at scale — anomalous in most environments
Admin share enumeration via CimInstance calls — should trigger an alert if not expected

The most significant finding was that both AV and firewall controls performed correctly under default configuration. The bypass only succeeded after manually disabling both — reinforcing that endpoint hardening and proper firewall policy are effective first-line controls.

Tools Used

MITRE CALDERA
Sandcat agent
MITRE ATT&CK Framework
Windows PowerShell
VirtualBox


Skills Demonstrated

C2 infrastructure deployment and agent management
ATT&CK-aligned TTP execution and monitoring
Endpoint security bypass analysis in a lab context
Blue team gap identification from adversary simulation findings
Technical documentation and security reporting


Related Projects

Splunk Detection Engineering Lab
Memory Forensics with Volatility
Burp Suite Web Application Testing Lab



Disclaimer: All activity was performed in an isolated virtual environment for educational purposes as part of coursework at George Brown College. No real systems or networks were targeted.
