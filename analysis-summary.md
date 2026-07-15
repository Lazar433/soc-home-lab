# Analysis Summary

## Overview

This document summarizes the three simulated incidents in this SOC lab, compares their detection mechanisms, and captures the overall lessons learned from building and operating a Wazuh SIEM deployment end-to-end - from infrastructure provisioning through attack simulation and detection analysis.

## Incident comparison

| # | Incident | Attacker technique | Detection type | Highest rule level | Required Sysmon? |
| --- | --- | --- | --- | --- | --- |
| 01 | Brute-force RDP | Wordlist credential guessing (Hydra) | Correlation (multiple events, time window) | 10 | No (Windows Security log) |
| 02 | Unauthorized admin account | Local account creation + group escalation | Single-event, high-sensitivity | 8 | No (Windows Security log) |
| 03 | Encoded PowerShell | Base64-obfuscated command execution | Process telemetry + artifact detection | 15 | Yes (Sysmon Operational) |

## MITRE ATT&CK coverage

| Tactic | Technique | Incident |
| --- | --- | --- |
| Credential Access | T1110 - Brute Force | 01 |
| Persistence | T1136.001 - Create Account: Local Account | 02 |
| Defense Evasion | T1027 - Obfuscated Files or Information | 03 |
| Execution | T1059.001 - PowerShell | 03 |
| Discovery | T1082 - System Information Discovery | 03 |
| Discovery | T1087 - Account Discovery | 03 |
| Persistence / Privilege Escalation | T1078 - Valid Accounts | 01, 02, 03 |

Across the three incidents, the attack chain progressed through a realistic sequence: initial access via credential compromise (01), persistence via a secondary account (02), and post-compromise discovery using an obfuscation technique (03). All three used the same account chain established in the earlier incidents rather than being isolated, unconnected tests.

## Key findings

1. **Wazuh's default ruleset covers common attack patterns without customization.** All three incidents were detected using out-of-the-box Wazuh rules - no custom correlation rules were required at any point in this lab.
2. **Detection depth depends entirely on telemetry source configuration.** Incidents 01 and 02 were fully visible through standard Windows Security event logs alone. Incident 03 required Sysmon process-creation telemetry, which was not forwarded to the Wazuh manager until the agent's ossec.conf was explicitly updated to include the Sysmon Operational event channel. This was the single most significant technical finding of the lab: a SIEM can appear fully operational (agent Active, other event types flowing normally) while silently missing an entire category of detections due to an incomplete log source configuration.
3. **Obfuscation hides payload content, not attacker behavior.** The -EncodedCommand flag remained fully visible in process telemetry even though its Base64 payload was not human-readable, and a secondary forensic artifact (__PSScriptPolicyTest) provided an independent detection opportunity. This reinforces that behavioral/process-level detection is more resilient than simple keyword matching against command content.
4. **Built-in OS security controls complement SIEM detection.** Windows' native account lockout policy independently interrupted the brute-force attack in Incident 01, and this was itself visible as a correlated Wazuh alert - demonstrating defense in depth even in a minimal lab environment.

## What I would do differently

**1. Verify every telemetry source right after agent enrollment, not just the agent status.**
Seeing the agent marked "Active" in the dashboard was not enough proof that everything was working. Standard Windows login events came through fine, but Sysmon data silently did not - this was only discovered while working on the third incident. Next time: send one test event from each expected source immediately after enrollment, before relying on the setup for anything else.

**2. Prepare all attack materials before the paid server is running.**
The server bills by the hour from the moment it exists. Building the password wordlist happened after the server was already up, and a mistake in that file wasted paid server time while it was being debugged. Next time: finish anything that does not require the live server - wordlists, payloads, test commands - before provisioning it.

**3. Take a VM snapshot as soon as the setup is confirmed working.**
VirtualBox can save the exact current state of a VM as a snapshot. Once the agent was confirmed working and Sysmon data was flowing, no snapshot was taken. When a later host restart caused a clock desync and broke the agent's registration, the only option was to manually re-enroll it from scratch, which took real time. Next time: snapshot immediately after confirming a working state, so any later issue can be undone in seconds instead of redone by hand.

These are the kind of details that matter beyond just completing the lab - they show the ability to recognize what went wrong and improve the process for next time, not just execute a checklist.

## Conclusion

This lab provided hands-on experience with the full SIEM lifecycle: cloud infrastructure provisioning, SIEM deployment and hardening, endpoint telemetry configuration, attack simulation across three distinct MITRE ATT&CK techniques, and detection analysis using both correlation rules and single-event alerting. The most valuable outcome was diagnosing and resolving a real telemetry gap (missing Sysmon channel) rather than working with a pre-configured, fully-functional environment - a scenario directly relevant to real-world SOC analyst and SIEM engineering work.
