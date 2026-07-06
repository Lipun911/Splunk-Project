# Splunk SIEM & Log Analysis Project

This project demonstrates practical SIEM workflow and incident response processes using Splunk, with a focus on the **end-to-end security operations workflow** rather than advanced SPL optimization.

---

## Project Overview

### What This Project Covers
- **Log Ingestion & Data Sources**: Configuring Splunk to ingest logs from multiple sources
- **Alert Creation & Management**: Building alerts that detect real threats with minimal false positives
- **Incident Response Workflow**: Following structured processes to detect, analyze, and respond to security incidents
- **Threat Intelligence Integration**: Using GeoIP and geographic data to enrich threat analysis
- **Dashboard Design**: Creating visualizations for SOC team monitoring
- **Detection Rules**: Understanding detection logic and tuning thresholds
- **Metrics & KPIs**: Measuring SIEM effectiveness (MTTD, MTTR, detection accuracy)

### My Strengths
✅ **Splunk Workflow** - Comfortable with data ingestion, search fundamentals, and alert configuration  
✅ **Incident Response** - Can follow structured response processes and create runbooks  
✅ **Log Analysis** - Understanding patterns, anomalies, and threat indicators  
✅ **Alert Tuning** - Reducing false positives while improving detection quality  
✅ **Communication** - Documenting findings and collaborating with security teams  

### Learning Areas
🔄 **SPL Query Optimization** - Working on more complex correlation queries  
🔄 **Advanced Analytics** - Building machine learning-based detections  
🔄 **Performance Tuning** - Optimizing searches for large datasets  

---

## Prerequisites
- **VirtualBox** installed
- **Kali Linux** as an attacker machine (for simulation)
- **Splunk** installed and configured
- Basic knowledge of Linux commands and log formats
- Sample log dataset (included in `sample-data/`)

---

## Project Structure

```
Splunk-Project/
├── README.md                              # This file
├── CASE_STUDY.md                          # Real-world attack scenario walkthrough
├── IMPLEMENTATION_GUIDE.md                # Step-by-step setup instructions
├── METRICS.md                             # KPIs and performance measurements
├── queries/
│   ├── basic-searches.spl                # Core SPL queries for log analysis
│   └── advanced-correlation.spl          # Reference queries for threat detection
├── alerts/
│   ├── ssh-brute-force-alert.conf        # SSH brute force detection
│   ├── failed-login-threshold-alert.conf # Failed login monitoring
│   └── geoip-anomaly-alert.conf          # Geographic anomaly detection
├── detection-rules/
│   ├── brute-force-detection-rule.md     # SSH brute force detection logic
│   └── lateral-movement-detection-rule.md # Lateral movement detection
├── dashboards/
│   └── security-monitoring-dashboard.json # Ready-to-import dashboard
└── sample-data/
    └── sample-auth-logs.txt              # Test data for running queries
```

---

## Step 1: Setting Up Splunk
1. Download and install Splunk Enterprise (or use Docker)
2. Start Splunk services and log in to the web interface
3. Verify connectivity to target log sources

**Screenshot:**
![Splunk Setup](https://github.com/user-attachments/assets/a12eb452-3a4b-4363-a588-d3acef8f6776)

---

## Step 2: Ingesting Logs into Splunk
1. Navigate to **Settings > Data Inputs**
2. Select the log source type (e.g., syslog, file upload)
3. Configure source type, index, and parsing settings
4. Verify that logs are being ingested by running a basic search: `index=* | head 10`

**Screenshot:**
![Log Ingestion](https://github.com/user-attachments/assets/64eb312f-ee06-4b15-9c34-8ac75c6c17d0)

---

## Step 3: Running Basic Splunk Searches

### Using Pre-Built Queries
Open `queries/basic-searches.spl` for ready-to-use searches. Run these in Splunk:

**Find Failed SSH Login Attempts:**
```spl
index=* sourcetype="/var/log/auth.log" "Failed password"
| stats count as failed_attempts by src_ip, user
| where failed_attempts > 5
| table src_ip, user, failed_attempts
```

**Detect SSH Brute Force Attacks:**
```spl
index=* sourcetype="/var/log/auth.log" "Invalid user"
| stats count as invalid_attempts by src_ip
| where invalid_attempts > 10
| table src_ip, invalid_attempts
```

### Understanding the Workflow
1. **Search**: Run queries to find suspicious patterns
2. **Analyze**: Look for trends, frequency, and geographic anomalies
3. **Correlate**: Connect related events to identify attack progression
4. **Alert**: Set thresholds for automated detection

**Screenshot:**
![Splunk Search](https://github.com/user-attachments/assets/e39cdea6-3ee4-4865-a24b-449d55f4ab06)

---

## Step 4: Implementing Real-Time Alerting

### Creating Your First Alert
1. After running a search, click **Alert** (top right)
2. Name your alert: e.g., "SSH Brute Force Detection"
3. Set trigger condition: `Number of events > 10`
4. Set trigger frequency: `Every 5 minutes`
5. Configure actions: Email, Webhook, or Log to Summary Index

### Understanding Alert Tuning
The goal is finding the **sweet spot**:
- **Too Sensitive**: Many false positives → alert fatigue
- **Too Loose**: Misses real attacks
- **Just Right**: Catches threats without excessive noise

See `alerts/` directory for example configurations.

**Screenshot:**
![Real-Time Alert](https://github.com/user-attachments/assets/dc27f73e-4941-4f0d-8472-89a18248dfe9)

---

## Step 5: GeoIP Lookup for Threat Intelligence

### Why Geographic Data Matters
- Identify attacks from unexpected locations
- Support incident response (add context to investigations)
- Reduce false positives (whitelist trusted regions)

### Using GeoIP in Splunk
```spl
index=* sourcetype="/var/log/auth.log" "Failed password"
| iplocation src_ip
| where country != "US"
| stats count by src_ip, country, city
```

### What This Tells You
- Attacks from China, Russia, etc. may need faster response
- Users logging in from home country are likely legitimate
- Multiple countries in one session could indicate compromise

**Screenshot:**
![GeoIP Lookup](https://github.com/user-attachments/assets/982fa487-1975-4436-b5b6-ff94c45f8ce0)

---

## Step 6: Attack Simulation with Kali Linux

### Running the Simulation
On your Kali Linux machine, simulate SSH brute force:
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://<target_IP>
```

### What to Look For in Splunk
After running the attack, search for:
```spl
index=* sourcetype="/var/log/auth.log" "Failed password" | stats count by src_ip
```

### Questions to Answer
1. ✅ Does Splunk detect the attack?
2. ✅ How long does it take to trigger the alert?
3. ✅ Does the alert have enough context for incident response?
4. ✅ Is the response time acceptable?

This practical test validates your entire SIEM setup.

**Screenshot:**
![Kali Linux Attack](https://github.com/user-attachments/assets/72cd263e-e043-468f-b72d-78464066e1be)

---

## Step 7: Visualizing Security Trends

### Creating Dashboards
1. Go to **Create > Dashboard**
2. Add panels for each metric:
   - Failed login attempts over time
   - Top attack sources by country
   - Attack volume by hour
   - Alert status summary

### Why Dashboards Matter for SOC
- Quick situational awareness for on-call analysts
- Executive reporting on security posture
- Identifying trends and patterns over time
- Validating alert performance

See `dashboards/security-monitoring-dashboard.json` for a complete example.

**Screenshot:**
![Splunk Visualization](https://github.com/user-attachments/assets/e839a05f-6465-49d4-9c35-0b5d0cdb8c8e)

---

## Understanding Detection Rules

### What is a Detection Rule?
A documented procedure for identifying a specific type of attack.

### Components
1. **Rule ID** - Unique identifier (e.g., BF-SSH-001)
2. **Description** - What attack it detects
3. **Logic** - The conditions that trigger it
4. **Severity** - How serious is this threat
5. **Response Steps** - What to do when triggered
6. **Tuning Parameters** - How to adjust for your environment

See `detection-rules/` for examples with full documentation.

---

## Real-World Case Study

**File:** `CASE_STUDY.md`

This document walks through a complete incident:
- ✅ How a real SSH brute force attack was detected
- ✅ Step-by-step analysis and response
- ✅ Response times and effectiveness metrics
- ✅ What worked and what to improve

**Key Takeaway:** This shows the end-to-end workflow from detection to resolution.

---

## Measuring SIEM Effectiveness

### Key Metrics
See `METRICS.md` for detailed breakdown of:

| Metric | What It Means | Target |
|--------|---------------|--------|
| **MTTD** | Time from attack to detection | < 5 min |
| **MTTR** | Time from alert to response | < 10 min |
| **Detection Rate** | % of real attacks caught | > 95% |
| **False Positive Rate** | % of alerts that are noise | < 5% |

### Why This Matters
Recruiters want to see you think about **operational effectiveness**, not just "building alerts."

---

## Conclusion

This project demonstrates:
✅ Understanding of complete SIEM workflow (ingest → search → alert → respond)  
✅ Practical incident response processes  
✅ Ability to configure production-ready alerts  
✅ Knowledge of threat intelligence integration  
✅ Metrics-driven thinking about security operations  

**It shows you can operate Splunk competently and contribute to a SOC team immediately.**

---

## Next Steps & Learning Goals

### Short Term (Next 3 Months)
- [ ] Get comfortable with SPL syntax for complex queries
- [ ] Practice correlation rule writing
- [ ] Understand data normalization and CIM (Common Information Model)

### Medium Term (3-6 Months)
- [ ] Build custom detection rules for your environment
- [ ] Optimize queries for performance
- [ ] Implement machine learning-based detections

### Long Term (6+ Months)
- [ ] Integrate Splunk with other security tools (SOAR, threat feeds)
- [ ] Develop advanced analytics and baselining
- [ ] Lead incident response for critical events

---

## Resources for Learning SPL

- [Splunk Official Documentation](https://docs.splunk.com)
- [Splunk Query Language Reference](https://docs.splunk.com/Documentation/Splunk/latest/SearchReference/SearchLanguageReference)
- [Splunk Boss of the SOC (free training)](https://www.splunk.com/en_us/training/free-courses/boss-of-the-soc.html)
- [Query examples in `queries/` directory](./queries)

---

## How to Use This Repository

### For Learning
1. Start with `IMPLEMENTATION_GUIDE.md` for setup
2. Run queries from `queries/` on your sample data
3. Review `CASE_STUDY.md` to understand incident response workflow
4. Study detection rule documentation in `detection-rules/`

### For Interviews
- Explain the **workflow** (not just the technology)
- Discuss how you'd reduce false positives
- Talk about incident response processes
- Show you understand SIEM effectiveness metrics
- Be honest about areas you're learning (SPL optimization)

### For Portfolio
This project demonstrates:
- Hands-on Splunk experience
- Understanding of incident response
- Documentation and communication skills
- Operational thinking (metrics, tuning, best practices)

---

## 📌 Contributing & Feedback

This is a **learning project**. I welcome:
- Feedback on alert configurations
- Improvements to detection rules
- Better SPL query examples
- Real incident scenarios to document

Feel free to fork and improve!

---

## Disclaimer

Sample data and attack scenarios are for **educational purposes only**. Use responsibly in authorized environments only.
