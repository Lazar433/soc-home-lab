\# Windows Victim Setup



\## Goal



Deploy a Windows 10 VM to serve as the attack target, with dual network adapters for local attacker connectivity and internet access.



\## Steps



\- Created a Windows 10 Pro VM (4GB RAM, 2 CPU cores, 60GB dynamically allocated disk)

\- Configured two network adapters:

&#x20; - Adapter 1: Host-only Adapter (for connectivity with the Kali attacker VM)

&#x20; - Adapter 2: NAT (for internet access — required for Windows updates and later for the Wazuh agent to reach the remote SIEM)

\- Disabled all Windows privacy/telemetry options during setup (diagnostic data, tailored experiences, advertising ID, etc.) to reduce background network noise that could interfere with attack-related log analysis

\- Verified network connectivity via `ipconfig`:

&#x20; - Host-only adapter: `192.168.56.104`

&#x20; - NAT adapter: `10.0.3.15`



!\[Windows 10 desktop and network configuration](../screenshots/03-windows/01-windows10-desktop-network-config.png)



\## Issues encountered



\- Initial installation attempt used VirtualBox's Unattended Install feature and hung indefinitely at "Getting files ready for installation." Investigation revealed the VM was running in a degraded fallback mode (Hyper-V Paravirtualization Interface, Nested Paging: Inactive) instead of using native hardware virtualization.

\- Root cause: Windows Hyper-V was active on the host, competing with VirtualBox for VT-x/AMD-V hardware virtualization access.

\- Fix: disabled Hyper-V on the host with `bcdedit /set hypervisorlaunchtype off` (as Administrator), followed by a host reboot.

\- The VM was rebuilt from scratch without the Unattended Install feature (manual installation instead), which also avoided a secondary issue where the auto-generated unattended installation media left the VM without a valid bootable OS.



\## Result



Windows 10 Pro VM running with dual network connectivity confirmed (Host-only + NAT). Ready for Sysmon and Wazuh agent installation.

