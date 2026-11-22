# Day 7: Custom Wazuh Decoder and the Full Log Pipeline

## Part 1: Custom Wazuh decoder and rule

I wrote a decoder for a made up app log:

```
2026-06-15T10:22:01 [AUTH] user=admin src=192.168.1.50 action=LOGIN_FAILED
```

It pulls out the timestamp, user, source IP and action. I also wrote three rules. The last one (100102) fires when the same IP fails to log in more than 5 times in 60 seconds. I tested it by writing 7 failed logins into a log file. It fired on the 6th one.

Things that went wrong:

- Wazuh would not take `\[` in the regex. I used `\p` instead to match the brackets.
- `action` is a built in field, so the rule could not use a `field` tag on it. I matched on the text instead.
- I put a `localfile` block in the wrong place in `ossec.conf` and the manager would not start. I moved it next to an existing one.

## Part 2: Grok pattern

I wrote a Grok pattern for an nginx log line and tested it on grokdebugger.

## Part 3: Full pipeline

nginx log file, then Filebeat, then Logstash, then Elasticsearch, then Kibana.

Things that went wrong:

- I left a `.bak` copy of the Logstash config in the pipeline folder. Logstash loaded both files, tried to use port 5044 twice and failed. I moved the backup out of the folder.
- The index name has to start with `logstash-`, because the `logstash_internal` user can only write to indices named like that.

## Files

- `local_decoder.xml` and `local_rules.xml`
- `logstash.conf`
- `filebeat-logstash.yml`
- `screenshots/` has the grokdebugger result, the logtest output, the alert and the Kibana view

