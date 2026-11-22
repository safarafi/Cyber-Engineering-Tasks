# Day 6: ELK Stack

I set up Elasticsearch and Kibana with Docker Compose (the docker-elk project) and used Filebeat to send logs into it.

## What I did

- Ran Elasticsearch and Kibana with docker-elk
- Used Filebeat to ship auth logs, nginx access logs and auditd logs
- Made some nginx logs with public IPs so the country panel has something to show
- Built a Kibana dashboard called "ELK SOC Lab Dashboard" with 3 panels: top countries by source IP, failed SSH attempts and HTTP error codes over time

## Problems

Kibana did not load from my laptop at first. The Discover page was also very slow on the VM, so I used a short time range.

## Files

- `screenshots/` has the dashboard

