# Lateral Movement Detection Rule

## Rule ID
LM-001

## Rule Title
Lateral Movement - Suspicious Authentication Pattern

## Description
Detects potential lateral movement by identifying users authenticating from multiple internal IPs within a short time period. Lateral movement is a key indicator of compromise where attackers move from one compromised host to another within the network.

## Severity
CRITICAL

## Detection Logic

### Conditions
1. **User authenticates from multiple IPs**: dc(src_ip) ≥ 3
2. **Time Window**: 10 minutes
3. **Network Scope**: Internal IPs only (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16)
4. **Destination Diversity**: dc(dest_ip) ≥ 2

### SPL Query
```spl
index=* sourcetype="/var/log/auth.log" "Accepted password"
| where src_ip IN(10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16)
| stats count as auth_count, dc(src_ip) as source_ips, values(src_ip) as source_list, dc(dest_ip) as dest_count by user, date_hour
| where source_ips >= 3 AND dest_count >= 2
| eval threat_score = (source_ips * 10) + (dest_count * 5)
```

## Alert Actions
- Immediate escalation to SOC Lead
- Isolate user account (disable temporarily)
- Trigger forensic investigation
- Collect netflow data for affected hosts

## Investigation Steps
1. Review authentication timeline for affected user
2. Check for privilege escalation attempts post-authentication
3. Analyze process execution logs on target systems
4. Review SMB/RPC activity between compromised hosts
5. Check for credential dumping (lsass.exe, mimikatz)

## False Positive Mitigation
- Whitelist VPN users (they appear from different IPs)
- Exclude service account activity
- Document normal multi-system admin access patterns
- Review jump host/bastion server activity

## Tuning Parameters
- `source_ips >= 3` — Reduce to 2 for more sensitive detection
- `time_window = 10m` — Reduce to 5m for strict environments
- `dest_count >= 2` — Adjust based on typical admin behavior

## Related Rules
- BF-SSH-001: SSH Brute Force Detection (initial access)
- PR-001: Privilege Escalation Detection
- DATA-EXFIL-001: Data Exfiltration Patterns