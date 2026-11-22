# Day 3: Wazuh Deployment

I installed Wazuh all in one (manager, indexer and dashboard) on my Ubuntu VM and connected two agents.

## What I did

- Installed Wazuh with the quick install script
- Registered the Kali VM as `kali-agent`
- Registered the Windows 10 VM as `CSLAB1`
- Both agents showed Active in the dashboard
- Ran 5 failed SSH logins against the Kali agent. Wazuh raised an alert for it and labelled it T1110 (brute force). The rule was 5712.

## Problems

At first the dashboard said "not ready", so I had to fix that before I could open it.

## Files

- `screenshots/` has the live dashboard, the two active agents and the T1110 alert

