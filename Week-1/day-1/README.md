# Day 1: Linux Hardening

I hardened a fresh Ubuntu 22.04 VM with manual CIS Level 1 style steps, then set up auditd to watch for changes.

## What I did

- Turned off root login over SSH
- Turned off password login over SSH
- Turned on the UFW firewall with incoming set to deny
- Checked for telnet, rsh and rlogin servers (none were installed)
- Installed auditd and loaded the Neo23x0 ruleset (205 rules)

## What I tested

I ran `sudo touch /etc/passwd` and found the event with `ausearch`. It showed up under the key `etcpasswd`. I also ran `su` twice, once with the right password and once with a wrong one, and both were logged.

## Not done yet

- Password rules in `pwquality.conf`
- Removing the telnet client
- Setting MaxAuthTries to 3
- Checking the audit event for an SSH login from another host
- Only 22 and 443 open in UFW. I have other ports open because of the Wazuh and Splunk labs.

## Files

- `checklist.md` has 16 controls, ticked or not ticked
- `auditd-annotated.rules` has the rules with a note on what each one watches
- `screenshots/` has the proof

