# Case Study: SSH Brute Force Attack Detection & Response

## Executive Summary
This case study demonstrates a real-world scenario of detecting and responding to an SSH brute force attack using Splunk SIEM with automated alerting and GeoIP threat intelligence.

---

## Attack Scenario

### Timeline
- **09:15 UTC** - Attack begins from external IP: `203.0.113.45` (Country: China)
- **09:20 UTC** - 10+ failed SSH attempts detected
- **09:21 UTC** - Alert triggered automatically
- **09:22 UTC** - Security team receives notification
- **09:25 UTC** - Manual verification and IP blocking
- **09:26 UTC** - Attack ceases

### Attack Details
- **Attack Type**: SSH Brute Force (Password guessing)
- **Source IP**: `203.0.113.45`
- **Target**: Production database server (10.0.1.50)
- **Targets Users**: admin, root, postgres
- **Tools Used**: Hydra with rockyou.txt wordlist
- **Duration**: 11 minutes
- **Total Attempts**: 247 failed login attempts

---

## Detection Process

### Step 1: Event Ingestion (09:15 UTC)
Splunk ingests syslog events from the target server:
```
[/var/log/auth.log]
Jan 15 09:15:32 db-prod sshd[12345]: Failed password for admin from 203.0.113.45 port 54321 ssh2
Jan 15 09:15:45 db-prod sshd[12346]: Failed password for admin from 203.0.113.45 port 54322 ssh2
Jan 15 09:15:58 db-prod sshd[12347]: Invalid user test from 203.0.113.45 port 54323 ssh2
... [247 events total]
```

### Step 2: Search Query Execution (Every 5 minutes)
Alert query runs and detects threshold breach:
```spl
index=* sourcetype="/var/log/auth.log" ("Failed password" OR "Invalid user")
| stats count as failed_attempts by src_ip, user
| where failed_attempts >= 10
```

**Result (09:20 UTC)**:
```
| src_ip        | user   | failed_attempts |
|---|---|---|
| 203.0.113.45  | admin  | 47              |
| 203.0.113.45  | root   | 89              |
| 203.0.113.45  | postgres | 55            |
```

**Status**: ✅ Threshold exceeded (10+) → Alert triggered

### Step 3: Alert Notification (09:21 UTC)
Automatic actions executed:
1. **Email Alert** sent to security team
2. **GeoIP Enrichment** applied to threat intelligence
3. **Webhook** triggered to incident management system

**Alert Content**:
```
Subject: ALERT: SSH Brute Force Attack Detected - 203.0.113.45

Body:
Potential SSH brute force attack detected:

Source IP: 203.0.113.45
Failed Attempts: 191
Time: 09:20 UTC
Geolocation: China, Beijing

Action: Block IP immediately or investigate.

Detection Confidence: HIGH
Risk Score: 95/100
```

### Step 4: Threat Intelligence Enrichment (09:20 UTC)
GeoIP lookup reveals suspicious location:
```spl
index=* sourcetype="/var/log/auth.log" "Failed password" src_ip="203.0.113.45"
| iplocation src_ip
| eval risk_level=case(country="US", "LOW", country="CN", "HIGH", 1=1, "MEDIUM")
```

**Results**:
```
| src_ip       | country | city    | latitude | longitude | risk_level |
|---|---|---|---|---|---|
| 203.0.113.45 | CN      | Beijing | 39.9042  | 116.4074  | HIGH       |
```

---

## Response Actions

### Immediate Actions (09:22 UTC)
✅ **Verified Alert Legitimacy**
- Cross-checked against known testing activities: NONE
- Confirmed external IP from threat intelligence: MALICIOUS

✅ **Blocked Attacker IP**
```bash
# Firewall rule added
iptables -A INPUT -s 203.0.113.45 -j DROP
```

✅ **Reviewed Authentication Logs**
```spl
index=* sourcetype="/var/log/auth.log" src_ip="203.0.113.45" "Accepted password"
```
**Result**: Zero successful logins from attacker IP ✅ (Attack prevented)

### Secondary Actions (09:25 UTC)
✅ **Checked for Lateral Movement**
```spl
index=* sourcetype="/var/log/auth.log" user IN(admin,root,postgres) 
| stats count as events by user, src_ip, dest_ip
| where src_ip!="10.0.0.0/8"
```
**Result**: No successful authentications for these users from external sources ✅

✅ **Account Security Review**
- admin account: Last successful login 5 days ago ✅
- root account: No recent logins ✅
- postgres account: Service account, no direct logins ✅

---

## Metrics & KPIs

| Metric | Value | Status |
|---|---|---|
| **Detection Time** | 5 minutes | ✅ EXCELLENT |
| **Response Time** | 3 minutes | ✅ EXCELLENT |
| **Time to Block** | 8 minutes | ✅ GOOD |
| **Successful Breach** | 0 | ✅ PREVENTED |
| **False Positives** | 0 | ✅ EXCELLENT |
| **MTTR** (Mean Time to Respond) | 8 minutes | ✅ EXCELLENT |

---

## Dashboard Visualization During Attack

### Real-Time Metrics
- **Failed Login Attempts**: 247
- **Unique Attack Sources**: 1 (203.0.113.45)
- **Active Alerts**: 1 (CRITICAL)
- **Affected Systems**: 1 (10.0.1.50)
- **Attack Status**: BLOCKED ✅

### Geographic Heat Map
- 🔴 **High Risk Zone**: China (Beijing)
- 🟢 **Organization Locations**: US (Normal activity)

---

## Key Learnings

1. **Early Detection Saved Infrastructure**
   - Alert triggered after just 10 failed attempts
   - Prevented brute force from escalating to successful compromise

2. **GeoIP Enrichment Added Context**
   - Immediately flagged China as suspicious for US-based org
   - Helped SOC team prioritize response

3. **Automated Response Would Be Better**
   - Manual IP blocking took 8 minutes
   - Automated firewall rules could block in <30 seconds
   - Recommendation: Implement auto-response for threshold-based attacks

4. **Alert Tuning is Critical**
   - 10 failed attempts in 5 minutes is good threshold
   - Prevents false positives while catching real attacks

---

## Remediation & Prevention

### Implemented Changes
1. **Enable SSH Key-Based Authentication Only** (disabled password auth)
2. **Implement Rate Limiting** (sshd_config: MaxAuthTries=3)
3. **Deploy Fail2Ban** for automatic blocking
4. **Enable MFA** for all administrative accounts
5. **Add GeoIP Restrictions** to SSH access

### Policy Updates
- Documented in Incident Response Playbook
- Added to Security Baseline Configuration
- Included in Security Awareness Training

---

## Conclusion
This case demonstrates Splunk's effectiveness in detecting and preventing SSH brute force attacks through:
- Real-time log analysis
- Automated alerting
- Threat intelligence enrichment (GeoIP)
- Rapid incident response

**Result**: Attack detected in 5 minutes, blocked in 8 minutes, zero successful breach. 🎯