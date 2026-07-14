\# Wazuh Server Setup



\## Overview



The Wazuh SIEM stack (manager, indexer, and dashboard) is deployed as a single all-in-one

installation on a Hetzner Cloud VPS. This server receives and analyzes security telemetry

from the Windows victim VM via the Wazuh agent.



\## Infrastructure



\- \*\*Provider:\*\* Hetzner Cloud

\- \*\*Server type:\*\* CPX32 (4 vCPU, 8 GB RAM, 160 GB disk)

\- \*\*OS:\*\* Ubuntu 22.04

\- \*\*Location:\*\* Nuremberg, Germany (nbg1)

\- \*\*Public IP:\*\* 46.225.111.144

\- \*\*Access:\*\* SSH key-based authentication only (ed25519 key pair)



Note: the originally planned CX32 (Cost-Optimized line) was unavailable at provisioning

time due to limited capacity across all regions. CPX32 (Regular Performance line) was used

instead, providing the same 4 vCPU / 8 GB RAM baseline with no availability issues.



!\[Hetzner server created](../screenshots/02-wazuh/01-hetzner-server-created.png)



\## Firewall configuration (UFW)



UFW was configured before installing Wazuh, following a default-deny policy. Only the

ports required for SSH access and Wazuh operation are open:



| Port | Purpose |

|------|---------|

| 22/tcp | SSH access |

| 443/tcp | Wazuh dashboard (HTTPS) |

| 1514/tcp | Agent-to-manager communication |

| 1515/tcp | Agent enrollment |



'''bash

sudo ufw allow 22/tcp

sudo ufw allow 443/tcp

sudo ufw allow 1514/tcp

sudo ufw allow 1515/tcp

sudo ufw enable

'''



!\[UFW firewall status](../screenshots/02-wazuh/02-ufw-firewall-status.png)



\## Wazuh installation



Wazuh 4.9.2 was installed using the official all-in-one install script, which deploys the

manager, indexer, and dashboard on a single node:



'''bash

curl -sO https://packages.wazuh.com/4.9/wazuh-install.sh \&\& sudo bash ./wazuh-install.sh -a

'''



Installation completed successfully in under 4 minutes, generating self-signed

certificates for all components and printing the initial admin credentials for the

dashboard.



!\[Wazuh install summary](../screenshots/02-wazuh/03-wazuh-install-summary.png)



\## Dashboard access



The Wazuh dashboard is accessible at 'https://46.225.111.144' using the admin credentials

generated during installation. The dashboard uses a self-signed certificate, so browsers

show a security warning that must be manually accepted for this lab environment.



!\[Wazuh dashboard](../screenshots/02-wazuh/04-wazuh-dashboard-login.png)



\## Agent enrollment



The Windows victim VM was enrolled as a Wazuh agent using the dashboard's built-in

deployment wizard (Server management > Endpoints Summary > Deploy new agent), which

generates a ready-to-use PowerShell installation command.



\- \*\*Agent name:\*\* windows-victim

\- \*\*Manager address:\*\* 46.225.111.144

\- \*\*Group:\*\* default



The agent was installed and started on the Windows victim VM:



'''powershell

Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.9.2-1.msi -OutFile $env:tmp\\wazuh-agent; msiexec.exe /i $env:tmp\\wazuh-agent /q WAZUH\_MANAGER='46.225.111.144' WAZUH\_AGENT\_NAME='windows-victim'

NET START WazuhSvc

'''



The agent connected successfully and shows as \*\*Active\*\* in the Wazuh dashboard, confirming

the full telemetry pipeline works end-to-end: Sysmon (Windows victim) → Wazuh agent →

Wazuh manager (Hetzner) → Wazuh dashboard.



!\[Agent active in Endpoints Summary](../screenshots/02-wazuh/06-agent-active-endpoints-summary.png)



\## Result



The SOC lab infrastructure is now fully operational:



\- Windows victim VM (local, VirtualBox)

\- Kali attacker VM (local, VirtualBox)

\- Wazuh SIEM server (Hetzner Cloud, remote)

\- Windows victim actively reporting telemetry to Wazuh



The environment is ready for the incident simulation phase.

