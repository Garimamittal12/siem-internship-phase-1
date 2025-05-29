SIEM Internship Phase 1  
**Threat Detection Environment on Linux using Splunk**

## 📌 Project Overview  
This project demonstrates how to detect common attacker behaviors on Linux systems using **Splunk Enterprise** and **Splunk Universal Forwarder**. Logs from Linux machines are collected and analyzed in real-time, allowing for detection of suspicious activity and generation of alerts.

---

## 🏗️ Architecture  
Logs from Linux endpoints are forwarded to Splunk Enterprise using Splunk Universal Forwarder. The log data is centralized and analyzed using Splunk’s search and alerting features.

The setup consists of the following components and data flow:

* **Linux VM 1 (Attacker/Tool)**

  * Simulates adversarial activity.
  * Generates logs from:

    * `syslog`
    * `auth.log`
    * `sysmon`
    * `audit.log`

* **Splunk Universal Forwarder**

  * Installed on the Linux VM.
  * Collects and forwards logs to the Splunk server.
  * Uses the `forward-server` configuration to send data.

* **Data Transmission**

  * Logs are sent over **TCP port 9997** to the Splunk indexer.

* **Splunk Enterprise**

  * Acts as the indexer, search head, and dashboard interface.
  * Runs a web interface on: `http://your_ip:8000`
  * Receives and processes logs for real-time monitoring and threat detection.

---

## 🔍 Threat Detection Scenarios  

### 🔄 Lateral Movement  
Detects when an attacker moves across systems within a network.  
**Monitors for:**
- SSH logins from one internal host to another
- Repeated logins between systems in a short time window
- Unusual internal IP login patterns  

**Log Sources:**  
- `/var/log/auth.log`  
- `/var/log/syslog`

**Detection Method:**  
Track SSH session activity and detect abnormal pivoting between systems.

---

### 🧹 Log Tampering  
Identifies attempts to delete or modify logs to cover malicious activity.  
**Monitors for:**
- Commands like `rm /var/log/*`, `echo > /var/log/auth.log`
- Use of `truncate`, `shred`, or redirection to log files

**Log Sources:**  
- `.bash_history`  
- `auditd`  
- `syslog`

**Detection Method:**  
Look for command patterns or file access related to critical system logs.

---

### 🕒 Suspicious Login After Hours  
Detects user logins outside normal working hours (e.g., 9 AM – 7 PM).  
**Log Source:**  
- `/var/log/auth.log`

**Detection Method:**  
Compare login timestamps against a defined working hour schedule to flag outliers.

---

### 👤 Unauthorized User Creation  
Alerts on the creation of new system users, which may indicate persistence techniques.  
**Monitors for:**
- Use of `useradd`, `adduser`
- Changes in `/etc/passwd`

**Log Sources:**  
- `auditd`  
- `auth.log`  
- `.bash_history`

**Detection Method:**  
Real-time tracking of user account creation or privilege escalation attempts.

---

### 🔐 Brute Force Attack Detection  
Detects brute force SSH login attempts followed by successful access.  
**Monitors for:**
- 10+ failed SSH logins from the same IP
- A successful login within 2 minutes of those failures  

**Log Source:**  
- `/var/log/auth.log`

**Detection Method:**  
Use `streamstats` in Splunk to correlate multiple login failures followed by a success.

---

## 🖥️ Splunk Universal Forwarder Integration  

**Log Files Monitored:**
- `/var/log/auth.log`
- `/var/log/syslog`
- `/var/log/audit/audit.log`
- `/home/*/.bash_history`

**Configuration Files:**
- `inputs.conf` – Specifies log files to monitor  
- `outputs.conf` – Defines connection to the Splunk indexer (TCP port 9997)

**Index:**  
All logs are sent to the `linux_logs` index using appropriate sourcetypes (`auth`, `syslog`, `auditd`).

---

## MITRE ATT&CK Alignment  
This detection environment maps to several MITRE ATT&CK tactics:
- **Persistence**
- **Defense Evasion**
- **Lateral Movement**
- **Credential Access**

These alignments provide meaningful context for threat detection and help build stronger incident response strategies.

