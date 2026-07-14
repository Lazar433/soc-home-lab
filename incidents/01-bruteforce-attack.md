\# Incident 01: Brute-Force RDP Attack



\## Summary



A brute-force attack was simulated against the RDP service (port 3389) on the Windows

victim VM, using a wordlist-based credential guessing attack from the Kali attacker VM.

The attack was successfully detected by Wazuh, which correlated multiple failed login

attempts and generated a high-severity alert.



\## Attack details



\- \*\*Attacker:\*\* Kali-Attacker VM (192.168.56.x)

\- \*\*Target:\*\* Windows victim VM (192.168.56.104), RDP service, port 3389

\- \*\*Target account:\*\* 'victim'

\- \*\*Tool:\*\* Hydra v9.7 (RDP module)

\- \*\*Method:\*\* Wordlist-based credential guessing (6-entry custom wordlist)



\### Command used



'''bash

hydra -l victim -P \~/passwords.txt rdp://192.168.56.104

'''



\### Result

