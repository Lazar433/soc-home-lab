\# Kali Attacker Setup



\## Goal



Deploy a Kali Linux VM as the attack machine, with connectivity to the Windows victim over the Host-only network and internet access for tool installation.



\## Steps



\- Created a Kali Linux VM (3GB RAM, 2 CPU cores, 40GB dynamically allocated disk), full GUI installation

\- Configured two network adapters:

&#x20; - Adapter 1: Host-only Adapter (for connectivity with the Windows victim VM)

&#x20; - Adapter 2: NAT (for internet access — package installation and updates)

\- Ran 'sudo apt update \&\& sudo apt upgrade' after first boot

\- Installed VirtualBox Guest Additions ('virtualbox-guest-x11') for shared clipboard support

\- Installed core attack tools: 'nmap', 'hydra', 'metasploit-framework'

&#x20; - Verified versions: Nmap 7.99, Hydra 9.7, Metasploit Framework 6.4.135-dev

\- Verified connectivity to the Windows victim VM via 'ping -c 4 192.168.56.104'



!\[Kali desktop](../screenshots/04-kali/01-kali-desktop.png)

!\[Successful ping to Windows victim](../screenshots/04-kali/02-ping-success.png)

!\[Attack tools installed](../screenshots/04-kali/03-tools-installed.png)



\## Issues encountered



\- After installing Guest Additions and rebooting, the VM's internal clock briefly desynchronized, causing corrupted/duplicate ping responses ('ping: Warning: time of day goes back'). Resolved automatically after the system re-synced with NTP; confirmed with 'date'.

\- Initial ping tests to the Windows victim VM failed (100% packet loss) despite correct IP addressing. Root cause: Windows Defender Firewall blocks inbound ICMPv4 Echo Request by default. Fixed by enabling the existing "File and Printer Sharing (Echo Request - ICMPv4-In)" firewall rule on the Windows victim VM via PowerShell ('Enable-NetFirewallRule').



\## Result



Kali Linux VM running with dual network connectivity confirmed, core attack tools installed, and verified network reachability to the Windows victim VM. Local infrastructure (SIEM pending, Windows victim, Kali attacker) is now complete and ready for incident simulation.

