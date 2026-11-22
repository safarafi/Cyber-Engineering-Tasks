# Day 8: MITRE ATT&CK Coverage and Gap Analysis

I used MITRE ATT&CK to check what my lab can see and what it can't.

## What I learned

- A tactic is the why. It is the goal the attacker has, like Credential Access.
- A technique is the how. Brute Force (T1110) is one way to get credentials.
- A sub-technique is the exact version of that, like Password Spraying (T1110.003).

I focused on six tactics: Initial Access, Execution, Persistence, Credential Access, Lateral Movement and Exfiltration.

## What I did

I made a layer in ATT&CK Navigator (Enterprise, v20, Linux and Windows) and coloured techniques using my Week 1 work:

- Green: I have a search or rule that gave the right result on test data (5 techniques)
- Blue: the logs are collected but I have no tested detection (7 techniques)
- Red: I can't see it because the log isn't collected (7 techniques)

Each technique has a comment with the evidence behind its colour.

## The 5 biggest gaps

1. T1003 OS Credential Dumping
2. T1547.001 Registry Run Keys
3. T1021.002 SMB and Admin Shares
4. T1567 Exfiltration Over Web Service
5. T1566 Phishing

The first two happen because Sysmon is installed on my Windows VM but the Wazuh agent doesn't send its log to the manager. It only sends the three default Windows logs. One config change would fix both, so that's the first thing I'd do.

## Things to know

- The PowerShell and WMI detections were tested on the TryHackMe dataset, not my own Windows VM.
- The exfiltration search ran on the fake firewall log I made, so the numbers are not real.
- Blue does not mean protected. It means the evidence is there if someone looks for it.

## Files

- `layer/lab-baseline-coverage.json` is the Navigator layer. In Navigator use Open Existing Layer, then Upload from Local.
- `detection-gap-analysis.md` is the written gap analysis
- `screenshots/navigator-matrix.png` is the matrix with the colours

