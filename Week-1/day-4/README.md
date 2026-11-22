# Day 4: Wazuh FIM, Vulnerability Detector and Dashboard

## File integrity monitoring

I turned on FIM on the Kali agent for `/etc`, `/bin` and `/usr/bin`. Then I changed `/etc/passwd` and Wazuh raised an alert for it.

## Vulnerability detector

The scan only found 4 findings on the Kali agent.

## Custom dashboard

I built a dashboard with these:

- top alert types by agent
- alerts over time
- source IPs (my lab IPs are private, so a world map does not show much)

I exported it from Saved objects. It comes out as an `.ndjson` file, not plain JSON.

## Files

- `dashboard-export.ndjson` is the exported dashboard
- `screenshots/` has the FIM alert, the findings and the dashboard

