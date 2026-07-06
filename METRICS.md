# Security Metrics & KPIs for SIEM Effectiveness

## Detection Metrics

### Mean Time to Detect (MTTD)
**Definition**: Average time between security event occurrence and detection
**Current Target**: < 5 minutes
**Measured By**: Alert trigger time - Event timestamp

**Example from Case Study**:
- Attack started: 09:15 UTC
- Detection: 09:20 UTC
- **MTTD: 5 minutes** ✅

### Detection Rate
**Definition**: Percentage of security events successfully detected
**Formula**: (Events Detected / Total Events) × 100
**Target**: > 95%

**Breakdown by Event Type**:
- Failed logins: 100% detection (247/247)
- Brute force attacks: 98% detection
- Lateral movement: 92% detection

---

## Response Metrics

### Mean Time to Respond (MTTR)
**Definition**: Average time from alert to action taken
**Current Target**: < 10 minutes
**Measured By**: Response action timestamp - Alert trigger timestamp

**Example from Case Study**:
- Alert triggered: 09:21 UTC
- IP blocked: 09:29 UTC
- **MTTR: 8 minutes** ✅

### Incident Resolution Time
**Definition**: Time from alert to incident closed/resolved
**Current Target**: < 30 minutes (CRITICAL), < 2 hours (HIGH)
**Measured By**: Incident close time - Alert time

**Current Performance**:
- CRITICAL incidents: Avg 12 minutes
- HIGH incidents: Avg 45 minutes
- MEDIUM incidents: Avg 4 hours

---

## Quality Metrics

### False Positive Rate
**Definition**: Alerts that trigger but are not real security incidents
**Formula**: (False Positives / Total Alerts) × 100
**Target**: < 5%

**Current Baseline**:
- SSH Brute Force rule: 2% false positives
- Lateral Movement rule: 1% false positives
- GeoIP anomaly rule: 8% false positives (needs tuning)

### Alert Accuracy (True Positive Rate)
**Definition**: Percentage of alerts representing real threats
**Formula**: (True Positives / Total Alerts) × 100
**Target**: > 95%

**Performance by Alert Type**:
- Brute force: 98% accuracy ✅
- Malware detection: 87% accuracy
- Data exfiltration: 92% accuracy

### Alert Noise Ratio
**Definition**: Average number of false alerts per true threat
**Target**: < 2 false alerts per 1 real threat
**Current**: 1.3:1 (acceptable)

---

## Operational Metrics

### Alert Volume
**Definition**: Total number of alerts generated
**Current**: 150-200 alerts/day
**Breakdown**:
- CRITICAL: 1-3/day
- HIGH: 5-10/day
- MEDIUM: 20-30/day
- LOW: 100-150/day

### Alert Burnout Threshold
**Definition**: Point where alert volume causes analyst fatigue
**Current Status**: ACCEPTABLE (< 300 alerts/day)
**Warning Level**: > 300 alerts/day (requires tuning)

### SOC Utilization
**Definition**: Percentage of analyst time spent on actual threats vs noise
**Target**: 80% real threats, 20% noise
**Current**: 75% real threats, 25% noise

---

## Threat Intelligence Metrics

### GeoIP Coverage
**Definition**: Percentage of events enriched with geographic data
**Current**: 98% (excellent)

### Threat Context Enrichment
**Definition**: Events correlated with threat intelligence feeds
**Current**: 65% enriched
**Target**: > 80%

### Detection Rule Coverage
**Definition**: Percentage of attack types covered by detection rules
**Current Coverage**:
- Brute force: 100% ✅
- Malware: 75%
- Data exfil: 60%
- Lateral movement: 80%
- Privilege escalation: 70%

---

## Dashboard Metrics Summary

| Metric | Target | Current | Status |
|--------|--------|---------|--------|
| MTTD | <5 min | 4.8 min | ✅ |
| MTTR | <10 min | 8.2 min | ✅ |
| Detection Rate | >95% | 97% | ✅ |
| False Positive Rate | <5% | 3.2% | ✅ |
| True Positive Rate | >95% | 96% | ✅ |
| Alert Accuracy | >95% | 94% | ✅ |
| SOC Efficiency | 80% | 75% | ⚠️ |
| Rule Coverage | >90% | 81% | ⚠️ |

---

## Improvement Roadmap

### Q1 2025
- [ ] Reduce GeoIP false positives from 8% to <3%
- [ ] Add data exfiltration detection rules
- [ ] Implement auto-response for known attack patterns

### Q2 2025
- [ ] Achieve 80%+ rule coverage across all threat types
- [ ] Implement ML-based anomaly detection
- [ ] Reduce MTTR to <5 minutes for CRITICAL alerts

### Q3 2025
- [ ] Integrate with additional threat feeds
- [ ] Deploy behavior analytics
- [ ] Achieve 99%+ detection rate

---

## Data Sources for Metrics
- Splunk alert logs (`index=main source=alerts`)
- Incident management system (JIRA Service Management)
- Firewall block logs
- System authentication logs
- Email logs (alert notifications)