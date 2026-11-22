# Day 2: Syslog, Sysmon and Windows Event IDs

I sent Linux logs to a central log server with rsyslog and put Sysmon on a Windows 10 VM.

## Syslog

rsyslog forwards `auth.log`, `syslog` and `kern.log`. The server saves them in `/var/log/remote/`, with one folder for each host.

## Sysmon

I installed Sysmon on the Windows 10 VM with the SwiftOnSecurity config. Event Viewer now shows Sysmon events like process creation, file creation, registry changes and DNS queries.

## Windows event IDs

I found real events for 4624, 4625, 4672, 4688, 4720 and Sysmon Event ID 1. I am still generating 4698, 4719, 7045 and 1102.

## Files

- `log-source-inventory.md` is a table of the log sources, their format, how they get to the server and what they can detect
- `screenshots/` has the proof

