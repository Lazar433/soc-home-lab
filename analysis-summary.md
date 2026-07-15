\# Analysis Summary



\## Overview



This document summarizes the three simulated incidents in this SOC lab, compares their detection mechanisms, and captures the overall lessons learned from building and operating a Wazuh SIEM deployment end-to-end - from infrastructure provisioning through attack simulation and detection analysis.



\## Incident comparison



| # | Incident | Attacker technique | Detection type | Highest rule level | Required Sysmon? |

| --- | --- | --- | --- | --- | --- |

| 01 | Brute-force RDP | Wordlist credential guessing (Hydra) | Correlation (multiple events, time window) | 10 | No (Windows Security log) |

| 02 | Unauthorized admin account | Local account creation + group escalation | Single-event, high-sensitivity | 8 | No (Windows Security log) |

| 03 | Encoded PowerShell | Base64-obfuscated command execution | Process telemetry + artifact detection | 15 | Yes (Sysmon Operational) |



\## MITRE ATT\&CK coverage



| Tactic | Technique | Incident |

| --- | --- | --- |

| Credential Access | T1110 - Brute Force | 01 |

| Persistence | T1136.001 - Create Account: Local Account | 02 |

| Defense Evasion | T1027 - Obfuscated Files or Information | 03 |

| Execution | T1059.001 - PowerShell | 03 |

| Discovery | T1082 - System Information Discovery | 03 |

| Discovery | T1087 - Account Discovery | 03 |

| Persistence / Privilege Escalation | T1078 - Valid Accounts | 01, 02, 03 |



Across the three incidents, the attack chain progressed through a realistic sequence: initial access via credential compromise (01), persistence via a secondary account (02), and post-compromise discovery using an obfuscation technique (03) - all performed using the account chain established in the earlier incidents rather than as isolated, unconnected tests.



\## Key findings



1\. \*\*Wazuh's default ruleset covers common attack patterns without customization.\*\* All three incidents were detected using out-of-the-box Wazuh rules - no custom correlation rules were required at any point in this lab.

2\. \*\*Detection depth depends entirely on telemetry source configuration.\*\* Incidents 01 and 02 were fully visible through standard Windows Security event logs alone. Incident 03 required Sysmon process-creation telemetry, which was not forwarded to the Wazuh manager until the agent's `ossec.conf` was explicitly updated to include the Sysmon Operational event channel. This was the single most significant technical finding of the lab: a SIEM can appear fully operational (agent Active, other event types flowing normally) while silently missing an entire category of detections due to an incomplete log source configuration.

3\. \*\*Obfuscation hides payload content, not attacker behavior.\*\* The `-EncodedCommand` flag remained fully visible in process telemetry even though its Base64 payload was not human-readable, and a secondary forensic artifact (`\_\_PSScriptPolicyTest`) provided an independent detection opportunity. This reinforces that behavioral/process-level detection is more resilient than simple keyword matching against command content.

4\. \*\*Built-in OS security controls complement SIEM detection.\*\* Windows' native account lockout policy independently interrupted the brute-force attack in Incident 01, and this was itself visible as a correlated Wazuh alert - demonstrating defense in depth even in a minimal lab environment.



\## What I would do differently



\- \*\*Validate all relevant event channels immediately after agent enrollment\*\*, rather than assuming default Windows Security log coverage is sufficient. A quick test event (e.g., a single Sysmon-logged process) should be part of the standard deployment checklist going forward.

\- \*\*Build the custom wordlist and account credentials before infrastructure provisioning\*\*, to reduce paid server uptime during initial trial-and-error (e.g., NLA and account lockout troubleshooting in Incident 01).

\- \*\*Snapshot the Windows victim VM after Sysmon/agent configuration is confirmed working\*\*, to allow faster recovery from clock desync or agent registration issues encountered after host restarts, instead of manually re-enrolling each time.



\## Conclusion



This lab provided hands-on experience with the full SIEM lifecycle: cloud infrastructure provisioning, SIEM deployment and hardening, endpoint telemetry configuration, attack simulation across three distinct MITRE ATT\&CK techniques, and detection analysis using both correlation rules and single-event alerting. The most valuable outcome was diagnosing and resolving a real telemetry gap (missing Sysmon channel) rather than working with a pre-configured, fully-functional environment - a scenario directly relevant to real-world SOC analyst and SIEM engineering work.



