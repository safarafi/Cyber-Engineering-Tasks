# Day 5: Splunk

I installed Splunk on my Ubuntu VM, loaded some logs and wrote 10 searches. Each one is in the cheat sheet with the query, the MITRE technique, the result and a screenshot.

## What I did

- Made three indexes: `linux`, `windows` and `firewall`
- Loaded my own `auth.log`
- Made a fake firewall log with 5,000 events using a Python script and loaded that too
- Wrote and tested 10 searches

## Problems

My own Windows export from CSLAB1 kept failing to upload. So the 6 Windows searches (2, 3, 4, 6, 7 and 10) ran on the TryHackMe Splunk instance instead. Searches 1, 5, 8 and 9 ran on my own Splunk. Searches 2 and 7 found nothing in the TryHackMe data, so I checked on CSLAB1 that those events exist (Appendix A in the cheat sheet).

## Files

- `Splunk_SPL_Cheat_Sheet_Day5.pdf` is the cheat sheet

