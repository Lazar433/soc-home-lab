\# Infrastructure Setup



\## Goal



Set up the local virtualization environment for the SOC lab: VirtualBox, an isolated network for the victim and attacker machines, and the base VMs.



\## Steps



\### VirtualBox installation



\- Installed VirtualBox 7.2.12 with Extension Pack

\- Set default VM folder to `D:\\SOC-Lab-VMs` (separate from the git-tracked documentation repo, to avoid committing large binary files)



\### Host-Only network



\- Created a Host-Only network via VirtualBox Network Manager

\- Used default settings (adapter name `VirtualBox Host-Only Ethernet Adapter`, DHCP enabled)

\- This network isolates the victim and attacker VMs from the home network, while allowing them to communicate with each other



!\[Host-Only network configuration](../screenshots/01-infrastructure/01-hostonly-network-config.png)



\## Issues encountered



A Hyper-V/VirtualBox conflict was encountered during initial VM creation - see docs/03-windows-victim-setup.md for details and resolution.



\## Result



Isolated local network ready. Both the Windows victim and Kali attacker VMs (see docs/03-windows-victim-setup.md and docs/04-kali-attacker-setup.md) are connected to this network, with confirmed bidirectional connectivity.



