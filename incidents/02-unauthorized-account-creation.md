\# Incident 02: Unauthorized Local Admin Account Creation



\## Summary



Following the successful RDP brute-force attack (Incident 01), the compromised `victim` account was used to simulate a post-exploitation persistence technique: creating a new local administrator account. This ensures continued access even if the original compromised credential is later discovered and changed. Wazuh detected both the account creation and its addition to the local Administrators group.



\## Attack details



\- \*\*Attacker access:\*\* RDP session as `victim` (credential obtained in Incident 01)

\- \*\*Target:\*\* Windows victim VM

\- \*\*Action:\*\* local account creation + privilege escalation via group membership

\- \*\*Technique:\*\* MITRE ATT\&CK T1136.001 - Create Account: Local Account



\### Commands used (inside the compromised RDP session, elevated CMD)



```cmd

net user hacker P@ssw0rd123! /add

net localgroup administrators hacker /add

```



Verification:



```cmd

net user hacker

```



!\[Account creation commands](../screenshots/06-account-creation/01-net-user-add-command.png)



\## Detection in Wazuh



Wazuh's default ruleset detected both actions as separate, individually-triggered alerts (no correlation/aggregation needed, unlike the brute-force pattern in Incident 01 -each event is sensitive enough on its own to warrant an alert):



| Event | Rule level | Description |

| --- | --- | --- |

| Local account created (Windows Event ID 4720) | 8 | "A user account was created" |

| Account added to Administrators group (Windows Event ID 4732) | 8 | "A member was added to a security-enabled local group" |



Alert detail confirmed the full forensic trail:

\- `subjectUserName: victim` -the account that performed the action

\- `targetUserName: hacker` -the account that was created/modified



This subject/target distinction is what allows a SOC analyst to trace the action back to the specific compromised account responsible, linking this incident directly to Incident 01.



!\[Events filtered by rule level](../screenshots/06-account-creation/02-events-rule-level-filter.png)



!\[Alert detail - subject and target user](../screenshots/06-account-creation/03-alert-detail-flyout.png)



\## Key takeaways



\- Unlike the brute-force detection (which relied on correlating multiple events over a time window), account creation and privilege group changes are sensitive enough to generate an alert from a single event -no pattern-matching required.

\- Wazuh's subject/target user fields provide direct attribution, connecting this incident to the account compromised in Incident 01 and demonstrating a realistic attack chain: initial access (brute-force) → persistence (rogue admin account).

\- In a real environment, a deliberately inconspicuous account name (mimicking a service account) would be used instead of an obviously suspicious name like "hacker" - naming choice is itself a detection evasion consideration worth noting.



\## MITRE ATT\&CK mapping



\- \*\*T1136.001 - Create Account: Local Account\*\*

\- \*\*T1078 - Valid Accounts\*\* (the account used to perform the action was itself previously compromised)

