# Splunk SIEM & Log Analysis Project

This project focuses on setting up Splunk for Security Information and Event Management (SIEM) and log analysis. It includes real-time alerting, GeoIP lookup, attack simulations, and visualization.

---

## Prerequisites
- **VirtualBox** installed
- **Kali Linux** as an attacker machine
- **Splunk** installed and configured
- Basic knowledge of Linux commands
- Access to a sample log dataset

---

## Step 1: Setting Up Splunk
1. Download and install Splunk Enterprise.
2. Start Splunk services and log in to the web interface.
3. Add your log data source.

**Screenshot:**
Splunk Setup![1_hVW5D1K5aalIu8RooGspTA](https://github.com/user-attachments/assets/a12eb452-3a4b-4363-a588-d3acef8f6776)

---

## Step 2: Ingesting Logs into Splunk
1. Navigate to **Settings > Data Inputs**.
2. Select the log source type (e.g., syslog, file upload).
3. Configure source type, index, and parsing settings.
4. Verify that logs are being ingested.

**Screenshot:**
Log Ingestion

![ingestion_3](https://github.com/user-attachments/assets/64eb312f-ee06-4b15-9c34-8ac75c6c17d0)

---

## Step 3: Running Basic Splunk Searches
1. Open the **Search & Reporting** app.
2. Run simple queries like:
   ```
   index=* sourcetype="/var/log/auth.log" "Failed Password"
   ```
3. Analyze log patterns and trends.

**Screenshot:**
Splunk Search![IMG-20250403-WA0004](https://github.com/user-attachments/assets/e39cdea6-3ee4-4865-a24b-449d55f4ab06)

---

## Step 4: Implementing Real-Time Alerting
1. Go to **Alerts** and create a new alert.
2. Define a search query for suspicious activity.
3. Configure trigger conditions (e.g., threshold-based, scheduled alerts).
4. Set up email notifications or webhook alerts.

**Screenshot:**
Real-Time Alert![alert](https://github.com/user-attachments/assets/dc27f73e-4941-4f0d-8472-89a18248dfe9)

---

## Step 5: GeoIP Lookup for Threat Intelligence
1. Install the **Splunk Lookup File Editor** app.
2. Upload a GeoIP database.
3. Run a query to map IPs to geographical locations:
   ```
   index=* | eval geo=geoip(ip_address)
   ```

**Screenshot:**
GeoIP Lookup![Mapping Blog Image 4](https://github.com/user-attachments/assets/982fa487-1975-4436-b5b6-ff94c45f8ce0)

---

## Step 6: Attack Simulation with Kali Linux
1. Use **SSH Brute Force** to simulate network scans:
   ```
   hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://<your_target_IP>
   ```
2. Check if Splunk detects the attack.
3. Generate attack reports in Splunk.

**Screenshot:**
Kali Linux Attack Simulation ![SSH attack](https://github.com/user-attachments/assets/72cd263e-e043-468f-b72d-78464066e1be)

---

## Step 7: Visualizing Security Trends
1. Use the **Visualization** tab to create bar charts, pie charts, and time-series graphs.
2. Customize dashboards for security monitoring.

**Screenshot:**
Splunk Visualization ![Visuallize data](https://github.com/user-attachments/assets/e839a05f-6465-49d4-9c35-0b5d0cdb8c8e)

---


## Conclusion
This project demonstrates how Splunk can be used for real-time log analysis, security monitoring, and threat intelligence. It also helps in developing practical SIEM skills for cybersecurity roles.

---

## Next Steps
- Automate log analysis using **Splunk SPL queries**.
- Integrate Splunk with other security tools (e.g., Suricata, Zeek).
- Set up **correlation rules** for detecting complex threats.

---

### 📌 Feel free to fork and improve this project!
