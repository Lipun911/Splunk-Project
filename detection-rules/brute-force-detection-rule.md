# Brute Force Detection Rule

## Rule ID
BF-SSH-001

## Rule Title
SSH Brute Force Attack Detection

## Description
This rule detects SSH brute force attacks by monitoring failed authentication attempts from a single source IP. Multiple failed attempts within a short time window indicate an attacker is trying to guess valid credentials.

## Severity
HIGH

## Detection Logic

### Conditions
1. **Event Source**: `/var/log/auth.log`
2. **Failed Attempts Threshold**: ≥ 10 failed login attempts
3. **Time Window**: 5 minutes
4. **Grouping**: By source IP address

### SPL Query
```spl
index=* sourcetype="/var/log/auth.log" ("Failed password" OR "Invalid user")
| stats count as failed_attempts by src_ip, user
| where failed_attempts >= 10
| eval risk_score = min(failed_attempts * 5, 100)
```

## Alert Actions
- Send email notification to security team
- Block source IP automatically (if auto-response enabled)
- Create incident ticket in SOAR platform
- Log to summary index for correlation

## False Positive Mitigation
- Whitelist known test automation tools
- Exclude scheduled maintenance windows
- Implement user-based exceptions for service accounts
- Review baseline login patterns per user

## Response Steps
1. Verify alert is not a false positive
2. Check attacker's geographic location (GeoIP)
3. Review successful logins from same IP in past 24h
4. If compromised: Force password reset, enable MFA, audit account activity
5. Block IP at firewall level

## Tuning Parameters
- `failed_attempts >= 10` — Adjust based on environment (test: 3, production: 10)
- `time_window = 5m` — Can be reduced to 2m for stricter detection

## Related Rules
- BF-SSH-002: SSH Brute Force with Successful Login
- BF-RDP-001: RDP Brute Force Detection
- GEO-001: Impossible Travel Detection