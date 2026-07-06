# Splunk Implementation Guide for Security Analysts

## Quick Start (First 30 Minutes)

### 1. Import the Detection Rules
```bash
# Copy alert configurations to Splunk
cp alerts/*.conf $SPLUNK_HOME/etc/apps/search/local/

# Restart Splunk
$SPLUNK_HOME/bin/splunk restart
```

### 2. Load Sample Data
```bash
# Upload sample logs to Splunk
# Go to: Settings > Data Inputs > Files & Directories
# Upload: sample-data/sample-auth-logs.txt
# Set sourcetype: syslog
# Set index: main
```

### 3. Verify Alerts Are Running
```spl
index=_internal group=thruput series="db-prod" | stats sum(kb) as kb
```

---

## Configuring Alerts

### Email Notifications
1. Go to **Settings > Distributed Search > Email settings**
2. Configure SMTP:
   - Server: `smtp.gmail.com`
   - Port: `587`
   - Authentication: Enabled
3. Test configuration

### Webhook Integration (for SOAR/Incident Management)
1. Edit alert configuration file: `alerts/ssh-brute-force-alert.conf`
2. Update webhook URL:
   ```
   webhook.url = https://your-incident-platform.com/api/alerts
   ```
3. Configure API authentication (Bearer token or API key)

---

## Creating Custom Dashboards

### Step 1: Go to Dashboard Creator
Home > Create > Dashboard

### Step 2: Add Panels
1. Click **Add Panel**
2. Paste query from `queries/basic-searches.spl`
3. Select visualization type (Timechart, Geom, Table, etc.)
4. Set refresh interval (Real-time: 30s, Scheduled: 5m)

### Step 3: Example Dashboard Layout
```
┌─────────────────────────────────────┐
│ Failed Logins (Timechart)  │ Alert Count (Gauge) │
├─────────────────────────────────────┤
│ Attack Sources (Geom Map)           │
├─────────────────────────────────────┤
│ Top Attackers (Table)    │ Risk Score (Single) │
└─────────────────────────────────────┘
```

---

## Advanced: Machine Learning Detection

### Enable Anomaly Detection
```spl
index=* sourcetype="/var/log/auth.log"
| timechart count as login_attempts
| anomalies sensitivity=0.85
```

### Threshold-Based Baselining
```spl
index=* sourcetype="/var/log/auth.log"
| stats count as login_attempts by user, date_hour
| eventstats avg(login_attempts) as baseline, stdev(login_attempts) as std_dev
| eval z_score = (login_attempts - baseline) / std_dev
| where abs(z_score) > 2
```

---

## Troubleshooting

### Alert Not Triggering
**Problem**: Alert rule created but not running
**Solution**:
1. Check if alert is enabled: Settings > Alerts > Review alert status
2. Verify search query syntax: Press "Search" button in alert editor
3. Check schedule: Alerts > Edit > Schedule (should be */5 * * * *)

### Too Many False Positives
**Problem**: Alert triggers constantly for legitimate activity
**Solution**:
1. Increase threshold: `where failed_attempts >= 20` (vs 10)
2. Add whitelist: `| search src_ip!="192.168.1.*"`
3. Exclude test users: `| search user!="testadmin"`
4. Adjust time window: From 5 minutes to 10 minutes

### Data Not Being Ingested
**Problem**: Queries return no data
**Solution**:
1. Verify source is configured: Settings > Data Inputs > Files & Directories
2. Check index exists: `| eventcount index=main`
3. Verify sourcetype: `index=* | stats count by sourcetype`
4. Check time range: Expand search time to -7d or -30d

---

## Best Practices

### 1. Alert Tuning
- Start with broad rules (high threshold)
- Gradually reduce threshold as you tune
- Monitor false positive rate weekly
- Adjust based on business context

### 2. Documentation
- Document all custom rules in detection-rules/ directory
- Include: Rule ID, Logic, False Positive Mitigation, Response Steps
- Keep runbooks for each alert type

### 3. Performance Optimization
- Use summary indexing for frequently-run queries
- Schedule heavy queries during off-peak hours
- Limit search time ranges where possible
- Use lookup tables for IP enrichment (faster than iplocation)

### 4. Security Hygiene
- Limit alert visibility to authorized teams
- Encrypt webhook URLs and API keys
- Audit who has access to alerts and dashboards
- Enable MFA for Splunk admin accounts

---

## Useful Resources
- Splunk Documentation: https://docs.splunk.com
- SPL Query Language: https://docs.splunk.com/Documentation/Splunk/latest/SearchReference/SearchLanguageReference
- MITRE ATT&CK Framework: https://attack.mitre.org
- Sample Datasets: https://www.splunk.com/en_us/resources/sample-data.html