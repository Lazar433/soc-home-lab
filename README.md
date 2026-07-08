\# SOC Home Lab



A self-built Security Operations Center (SOC) lab designed to bridge the gap between certification theory (Security+, CySA+) and hands-on, real-world SOC analyst skills.



\## About this project



I'm an L1 IT Support professional (Active Directory account management, hardware/software, networking) transitioning into cybersecurity. This lab was built to gain practical experience deploying, configuring, and monitoring a SIEM, simulating real attacks, and investigating them the way a SOC analyst would on the job - not just reading about it.



\## Architecture



\- \*\*SIEM:\*\* Wazuh, hosted on a Hetzner Cloud VPS (Ubuntu 22.04)

\- \*\*Victim endpoint:\*\* Windows 10 VM with Sysmon, running locally in VirtualBox

\- \*\*Attacker machine:\*\* Kali Linux VM, running locally in VirtualBox

\- \*\*Networking:\*\* Local Host-Only network between victim and attacker; Wazuh agent communicates with the remote SIEM over the internet (ports 1514/1515)



\*(Architecture diagram to be added.)\*



\## Tools used



\- Wazuh (SIEM/XDR)

\- Sysmon (endpoint telemetry)

\- VirtualBox (virtualization)

\- Kali Linux (attack simulation - nmap, Hydra, Metasploit)

\- Hetzner Cloud (VPS hosting for SIEM)

\- Git / GitHub (version control and documentation)



\## Simulated incidents



1\. Brute-force RDP attack

2\. Unauthorized local admin account creation

3\. Encoded PowerShell execution



Each incident includes: what happened, how it was detected, the relevant MITRE ATT\&CK technique, and what the response would be in a production environment.



\## Ethical note



All activity in this lab was performed exclusively within an isolated, self-owned virtual environment. No third-party systems, networks, or production environments were involved at any point.



\## Documentation



\- \[Infrastructure Setup](docs/01-infrastructure-setup.md)

\- \[Wazuh VPS Deployment](docs/02-wazuh-vps-deployment.md)

\- \[Windows Victim Setup](docs/03-windows-victim-setup.md)

\- \[Kali Attacker Setup](docs/04-kali-attacker-setup.md)

\- \[Incident 1: Brute-Force Attack](incidents/01-bruteforce-attack.md)

\- \[Incident 2: Unauthorized Account Creation](incidents/02-unauthorized-account-creation.md)

\- \[Incident 3: Encoded PowerShell](incidents/03-encoded-powershell.md)

\- \[Analysis Summary](analysis-summary.md)



\## Status



🔧 In progress - this repository is being built and documented live, phase by phase.

