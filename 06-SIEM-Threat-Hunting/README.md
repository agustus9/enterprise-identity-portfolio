# 🔍 SIEM Engineering & Advanced Threat Hunting

## 📋 Business Scenario & Objective
Security teams are inundated with thousands of logs daily. Without structured correlation rules and efficient querying, critical indicators of compromise (IOCs) go unnoticed.

**The Solution:** I engineered detection and threat hunting infrastructure within **Splunk**, **Microsoft Sentinel**, and **IBM QRadar**. These queries detect unauthorized access, privilege escalation, and brute force attacks across the enterprise.

---

## 🛠️ Part 1: Splunk (SPL) Security Queries

### 1. Brute Force Detection followed by Successful Login
This search isolates a classic attack pattern: multiple failed logins followed by a successful authentication within a short timeframe.

```spl
index=security sourcetype=WinEventLog (EventCode=4625 OR EventCode=4624)

| stats count(eval(EventCode=4625)) as FailedCount, count(eval(EventCode=4624)) as SuccessCount by AccountName, src_ip
| where FailedCount >= 5 AND SuccessCount >= 1
```

### 2. Privilege Escalation on Windows Host
This query flags unusual process spawning for elevated administrative accounts.

```spl
index=security sourcetype="WinEventLog:Security" EventCode=4672 

| eval user_account=mvindex(Account_Name,1) 
| search "Security ID" NOT IN ("SYSTEM","LOCAL SERVICE","NETWORK SERVICE")
```

---

## 🛠️ Part 2: Microsoft Sentinel (KQL) Security Queries

### 1. Anomalous Administrative Sign-Ins (MFA Bypass attempts)
Using KQL, this query searches for login events where users accessed highly privileged roles from unexpected locations or without completed MFA.

```kql
SigninLogs

| where ResultType == 0
| where AppDisplayName == "Azure Portal" or AppDisplayName == "Microsoft Entra Admin Center"
| where AuthenticationRequirement == "singleFactorAuthentication"
| summarize count() by UserPrincipalName, IPAddress, Location
```

---

## 🛠️ Part 3: IBM QRadar (AQL) Detection Queries

### 1. High-Volume Firewall Denies from a Single IP
This Ariel Query Language (AQL) search surfaces external IPs performing port scans or trying to breach network borders.

```aql
SELECT sourceip, destinationip, destinationport, count(*) 
FROM events 
WHERE qid = 3000148 AND (protocolid = 6 OR protocolid = 17)
GROUP BY sourceip 
HAVING count(*) > 100 
ORDER BY count(*) DESC
LAST 24 HOURS
```

---

## 📉 Business Value & Risk Reduction
* **Reduced Time-to-Detection (TTD):** Replaced manual log review processes with real-time alerting queries.
* **Proactive Defense:** Identified early-stage brute force attacks before lateral movement occurred.
* **Complete Visibility:** Connected perimeter logs directly with host-level endpoint events for continuous monitoring.
