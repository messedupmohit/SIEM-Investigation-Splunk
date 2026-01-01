# 🛡️ SIEM Log Investigation – Mock SOC Investigation Using Splunk

## 📖 Overview

This project documents a **mock Security Operations Center (SOC) investigation** conducted using **Splunk SIEM** in a controlled lab environment. The investigation focuses on analysing Windows event logs to identify suspicious activity, determine indicators of compromise (IOCs), and assess the impact of a potential host compromise.

The goal of this project is to demonstrate **SIEM fundamentals, log analysis, and incident investigation skills** expected from an entry-level SOC / Security Analyst.

---

## 🎯 Objectives

- Analyse Windows event logs using a SIEM platform
- Detect suspicious and malicious behaviour
- Identify persistence mechanisms and attacker activity
- Extract and document Indicators of Compromise (IOCs)
- Produce a structured SOC-style investigation report

---

## 🧪 Lab Environment

- **Platform:** TryHackMe
- **SIEM Tool:** Splunk (Search & Reporting)
- **Log Sources:**

  - Windows Event Logs
  - PowerShell Operational Logs

- **Index Analysed:** `main`

---

## 🧠 Skills Demonstrated

- SIEM concepts and workflows
- SPL (Search Processing Language) queries
- Log correlation and analysis
- Incident investigation methodology
- IOC identification and documentation
- Security reporting and communication

---

## 📊 Investigation Summary

| Category               | Details                                       |
| ---------------------- | --------------------------------------------- |
| Total Events Analysed  | 12,256                                        |
| Affected Asset         | Windows Host                                  |
| Attack Technique       | Backdoor user creation & PowerShell execution |
| Persistence Identified | Yes                                           |
| Impact Level           | Host-level compromise                         |
| Investigation Outcome  | Confirmed malicious activity                  |

---

## 🔍 Investigation Walkthrough

### 1️⃣ Log Ingestion Validation

**Purpose:**
Verify that logs were successfully ingested into the SIEM for analysis.

**SPL Query:**

```spl
index=main
```

**Finding:**
12,256 events were present in the `main` index, confirming successful log ingestion.

📸 **Screenshot Placeholder:**
`screenshots/01_total_events.png`

---

### 2️⃣ Detection of Suspicious User Creation

**Purpose:**
Identify unauthorised or suspicious user account creation activity.

**SPL Query:**

```spl
index=main EventID=4720
```

**Finding:**
A new user account named **A1berto** was created. The username closely resembles an existing legitimate account, suggesting an impersonation attempt.

📸 **Screenshot Placeholder:**
`screenshots/02_user_creation.png`

---

### 3️⃣ Registry Modification Analysis

**Purpose:**
Determine whether registry changes were made to support persistence.

**SPL Query:**

```spl
index=main A1berto EventID=13
```

**Finding:**
A registry key associated with the newly created account was modified, indicating persistence-related activity.

**Registry Path Identified:**

```
HKLM\SAM\SAM\Domains\Account\Users\Names\A1berto
```

📸 **Screenshot Placeholder:**
`screenshots/03_registry_modification.png`

---

### 4️⃣ User Impersonation Assessment

**Purpose:**
Check whether an existing legitimate user was impersonated.

**Finding:**
Logs referenced a legitimate user named **Alberto**, supporting the hypothesis that the attacker attempted to blend malicious activity with legitimate account naming.

📸 **Screenshot Placeholder:**
`screenshots/04_user_impersonation.png`

---

### 5️⃣ Remote Command Execution Analysis

**Purpose:**
Identify how the backdoor user account was created.

**Finding:**
The following remote command was executed using WMIC to create the user account:

```
"C:\Windows\System32\Wbem\WMIC.exe" /node:WORKSTATION6 process call create
"net user /add A1berto paw0rd1"
```

This confirms unauthorised remote command execution.

📸 **Screenshot Placeholder:**
`screenshots/05_remote_command.png`

---

### 6️⃣ Login Activity Verification

**Purpose:**
Determine whether the backdoor account was actively used.

**SPL Query:**

```spl
index=main User="A1berto"
```

**Finding:**
No login attempts were observed for the backdoor account, indicating it may have been created for future use.

📸 **Screenshot Placeholder:**
`screenshots/06_login_attempts.png`

---

### 7️⃣ Suspicious PowerShell Activity Detection

**Purpose:**
Identify malicious PowerShell execution on the compromised host.

**SPL Query:**

```spl
index=main powershell
```

**Finding:**
Suspicious PowerShell activity was detected on host **James.browne**, indicating script-based malicious execution.

📸 **Screenshot Placeholder:**
`screenshots/07_powershell_activity.png`

---

### 8️⃣ PowerShell Event Volume Analysis

**Purpose:**
Assess the scale of PowerShell activity.

**SPL Query:**

```spl
index=main EventID=4103
```

**Finding:**
A total of **79 PowerShell events** were recorded, reinforcing evidence of malicious automation.

📸 **Screenshot Placeholder:**
`screenshots/08_powershell_count.png`

---

### 9️⃣ Encoded PowerShell Payload & Network Indicator Analysis

**Purpose:**
Reconstruct the external web request initiated by the malicious PowerShell script.

**Steps Performed:**

1. Identified encoded PowerShell content in logs
2. Decoded the payload
3. Extracted destination IP and URL
4. Defanged the indicator for safe reporting

📸 **Screenshot Placeholders:**

- Encoded payload in Splunk → `screenshots/09_encoded_payload.png`
- Decoded payload output → `screenshots/10_decoded_payload.png`
- Destination IP identified → `screenshots/11_finding_ip.png`
- Full URL reconstructed → `screenshots/12_complete_address.png`
- Defanged URL → `screenshots/13_defanged_ip.png`

**Final Defanged URL:**

```
hxxp://10.10.10.5/news.php
```

This confirms outbound communication consistent with command-and-control or payload retrieval behaviour.

---

## 🚨 Indicators of Compromise (IOCs)

| Type             | Value                                            |
| ---------------- | ------------------------------------------------ |
| Backdoor User    | A1berto                                          |
| Compromised Host | James.browne                                     |
| Registry Path    | HKLM\SAM\SAM\Domains\Account\Users\Names\A1berto |
| Malicious URL    | hxxp://10.10.10.5/news.php                       |

---

## 🛠️ Recommended Remediation Actions

- Remove and disable the malicious user account
- Reset credentials for affected and impersonated users
- Isolate or reimage the compromised host
- Block malicious network indicators
- Strengthen PowerShell logging and monitoring

---

## 📘 Conclusion

This project demonstrates a structured SOC-style investigation using Splunk SIEM to analyse Windows event logs, identify malicious behaviour, and document indicators of compromise. The investigation highlights common attacker techniques such as backdoor account creation, persistence via registry modification, and PowerShell-based execution.

The project reflects real-world SOC analyst responsibilities, including detection, analysis, and incident reporting.

---

## 📂 Repository Structure

```
SIEM-Log-Investigation-Splunk/
├── README.md
└── screenshots/
    ├── 01_total_events.png
    ├── 02_user_creation.png
    ├── 03_registry_modification.png
    ├── 04_user_impersonation.png
    ├── 05_remote_command.png
    ├── 06_login_attempts.png
    ├── 07_powershell_activity.png
    ├── 08_powershell_count.png
    ├── 09_encoded_payload.png
    ├── 10_decoded_payload.png
    ├── 11_finding_ip.png
    ├── 12_complete_address.png
    └── 13_defanged_ip.png
```

---
