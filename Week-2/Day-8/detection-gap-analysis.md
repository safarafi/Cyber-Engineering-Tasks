# Detection Gap Analysis

**Environment:** Ubuntu Server 22.04 (Wazuh manager, Splunk, ELK, auditd, rsyslog) and one Windows 10 endpoint (Wazuh agent, Sysmon)
**Framework:** MITRE ATT&CK Enterprise v20
**Companion file:** `layer/lab-baseline-coverage.json` (ATT&CK Navigator layer)


## How to read the coverage layer

| Colour | Meaning |
|---|---|
| Green | A search or rule returned the correct result on test data. The comment on each technique says which data. |
| Blue | Visibility only: the logs that would show the technique are collected, but no tested detection exists. |
| Red | Blind spot: no log source that would show the technique is collected centrally. |

Current baseline: 5 green, 7 blue, 7 red, across the tactics Initial Access, Execution, Persistence, Credential Access, Lateral Movement and Exfiltration.

Two limits on the green techniques: PowerShell and WMI (searches 6 and 3) were validated on the TryHackMe Windows dataset, not on the lab's own endpoint, and the exfiltration search (5) ran on a synthetic firewall log. Green means the detection logic works, not that it is running in production.

## Top 5 techniques that cannot currently be detected

| # | Technique | Tactic | Why it is invisible | Log source that fixes it |
|---|---|---|---|---|
| 1 | T1003 OS Credential Dumping | Credential Access (TA0006) | Sysmon is installed but its log is not sent to the Wazuh manager | Sysmon EID 10 (process access) |
| 2 | T1547.001 Registry Run Keys / Startup Folder | Persistence (TA0003) | Same cause: the registry change is only in the local Sysmon log | Sysmon EID 13 (registry value set) |
| 3 | T1021.002 SMB / Windows Admin Shares | Lateral Movement (TA0008) | No share-access auditing and no network flow logs | Windows Security 5140 and 5145, plus network sensor data |
| 4 | T1567 Exfiltration Over Web Service | Exfiltration (TA0010) | No proxy, web gateway or egress logs | Web proxy or secure web gateway logs |
| 5 | T1566 Phishing | Initial Access (TA0001) | No email logs of any kind | Mail gateway or mailbox audit and message-trace logs |

## Detail for each gap

### 1. T1003 OS Credential Dumping (Credential Access)

**Why it is a gap.** Stealing credentials from the memory of `lsass.exe` is a standard step after an attacker gets a foothold. The event that shows it is Sysmon EID 10, which records one process opening another. The Sysmon log exists on the Windows VM, but the Wazuh agent collects only three event channels (the default Windows set), and the Sysmon channel is not one of them. The event is written and never leaves the machine.

**What fixes it.** Add the Sysmon channel to the agent's collection (see the quick win below).

**What to detect once collected.** EID 10 where the target is `lsass.exe` and the process asking for access is not a known system or security tool, especially with a high access-rights value.

### 2. T1547.001 Registry Run Keys / Startup Folder (Persistence)

**Why it is a gap.** Writing a program into a Run key makes it start at every logon. The Windows Security log does not record registry changes unless special object auditing is set up. Sysmon EID 13 records them directly, but that log is not collected centrally, for the same reason as gap 1.

**What fixes it.** The same Sysmon change as gap 1. As a second route, Wazuh file integrity monitoring can watch the Run keys on the Windows agent.

**What to detect once collected.** EID 13 where the key path ends in `\CurrentVersion\Run` or `\RunOnce` and the value points to a user-writable folder such as AppData or Temp.

### 3. T1021.002 SMB / Windows Admin Shares (Lateral Movement)

**Why it is a gap.** Attackers copy tools to a second machine through shares such as `C$` and `ADMIN$`. The lab does not audit file-share access, so events 5140 and 5145 are never written, and there are no network flow logs that could show SMB traffic between hosts.

**What fixes it.** Turn on the "File Share" and "Detailed File Share" audit subcategories on Windows, collect Security events 5140 and 5145, and add a network source such as flow records or a network sensor that logs SMB (port 445).

**What to detect once collected.** Access to `ADMIN$` or `C$` from a workstation that does not normally administer other machines, followed by a service install (7045) or a remote process start.

### 4. T1567 Exfiltration Over Web Service (Exfiltration)

**Why it is a gap.** Data leaves as ordinary HTTPS to cloud storage or file-sharing sites, so it looks like normal browsing. The lab's only exfiltration detection (T1048, search 5) works from firewall byte counts per destination IP. It cannot say which service received the data, and it would not stand out against a large web service.

**What fixes it.** Web proxy or secure web gateway logs with the URL or domain, user, and bytes uploaded. Firewall logs with application identification, or DNS logs, are a weaker substitute.

**What to detect once collected.** A large volume uploaded by one user to a file-sharing or cloud-storage domain they have not used before, or outside working hours.

### 5. T1566 Phishing (Initial Access)

**Why it is a gap.** Most intrusions start with a malicious email, and the lab has no email source at all, so the first step of an attack is invisible. The nearest signal is downstream: an Office application starting a command shell, which the Windows process-creation events can show once command-line logging is on.

**What fixes it.** Logs from the client's mail platform: message trace, attachment and URL verdicts, and mailbox audit logs. Connect them to the SIEM through the platform's API or a log forwarder.

**What to detect once collected.** Messages with macro-enabled or executable attachments, links to newly registered domains, and a user clicking a link shortly before a suspicious process starts on their endpoint.

## Recommended order of work

1. **Quick win: forward the Sysmon log to the manager (closes gaps 1 and 2 in one change).** Add this to the Windows agent's `ossec.conf` and restart the agent service:
```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```
   This moves T1003 and T1547.001 from red to blue. It would also move T1071.004 (DNS, Sysmon EID 22) from red to blue. Writing the rules for each then turns them green.
2. **Enable file-share auditing** on the Windows endpoint (gap 3). It is a small configuration change.
3. **Add a web proxy or gateway log source** (gap 4). This needs a client decision on what egress path exists.
4. **Onboard the email platform** (gap 5). It has the largest effect on Initial Access, but it depends on the client's mail system.

## Other gaps noted

- **T1071.004 DNS:** the DNS events (Sysmon EID 22) stay on the endpoint, the same cause as gaps 1 and 2.
- **T1133 External Remote Services:** no VPN or remote-access gateway logs.

## Limits of this analysis

- The matrix is limited to a Linux server and one Windows endpoint. A real engagement would also cover identity (Active Directory or cloud sign-in logs), which is not in this lab.
- Blue does not mean protected. It means the evidence would be available to an analyst who looks for it.
- Colours reflect the lab as of the date above and should be revised when sources are added.
