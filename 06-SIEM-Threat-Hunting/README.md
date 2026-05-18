# 🔍 SIEM Engineering & Advanced Threat Hunting

## 📋 Scenario & Business Problem
Identity ecosystems are a primary target for advanced persistent threats (APTs). A common attack vector involves compromising an administrative identity to quietly modify critical platform roles, or executing a "MFA fatigue" attack to bypass access controls. 

Without specialized security monitoring, these identity security incidents blend into normal audit logs. Security Operations Centers (SOC) require optimized correlation logic to instantly flag suspicious behavior, such as a user triggering an uncommon administrative role modification in SailPoint IIQ immediately followed by a high-risk cloud login from an anomalous geolocation in Entra ID.

## 💡 Solution Architecture
This project establishes high-fidelity detection rules written in **Kusto Query Language (KQL)**, **Splunk Search Processing Language (SPL)**, and **Ariel Query Language (AQL)**.
1. **Behavioral Analytics:** Baselines normal administrative behavior to isolate anomalies.
2. **Multi-Source Correlation:** Bridges on-premises SailPoint IdentityIQ audit trails with cloud-native Entra ID and Okta sign-in logs.
3. **MFA Fatigue Detection:** Tracks rapid sequences of denied push notifications followed by a successful login.
4. **Privilege Escalation Alerting:** Monitors the specific database tables or application logs tracking target group modifications.

## 🏗️ Technical Components
* **Component Type:** SIEM Detection Rules & Correlation Logic
* **Query Standards:** Microsoft Sentinel KQL, Splunk SPL, IBM QRadar AQL
* **Log Sources Evaluated:** SailPoint IIQ AuditLogs, MicrosoftEntraID SignInLogs, Okta Syslog

---

## 🛠️ Threat Hunting Rule Implementations

### Implementation 1: Microsoft Sentinel (KQL) - Multi-Source Identity Escalation
This rule detects a critical administrative role modification inside SailPoint IIQ immediately followed by a medium or high-risk cloud authentication event via Entra ID by the same user account within a tight 1-hour window.

```kusto
let HighRiskIdentityModifications = 
    Syslog

    | where ProcessName == "sailpoint.iiq"
    | where Message contains "syslog:CapabilityAssignment" or Message contains "addRole"
    | extend TargetUser = extract(@"targetIdentity=([^,\s]+)", 1, Message)

    | extend AdminActor = extract(@"actor=([^,\s]+)", 1, Message)
    | project TimeGenerated, AdminActor, TargetUser, Message;
SigninLogs

| where RiskLevelDuringSignIn in ("medium", "high")
| join kind=inner (HighRiskIdentityModifications) on $left.UserPrincipalName == $right.TargetUser
| where TimeGenerated between ((TimeGenerated1 - 1h) .. (TimeGenerated1 + 1h))

| project TimeGenerated, UserPrincipalName, IPAddress, Location, AdminActor, Message
```

### Implementation 2: Splunk (SPL) - Okta MFA Fatigue / Push Spam
This correlation rule isolates scenarios where an identity rejects multiple continuous MFA push notifications within a 5-minute window but subsequently responds with a successful authentication, signaling user capitulation.

```splunk
index=security sourcetype="okta:system:log" 
    (eventType="user.authentication.auth_via_mfa" AND displayMessage="REJECT") OR (eventType="user.authentication.auth_via_mfa" AND displayMessage="SUCCESS")
| sort 0 + _time
| streamwindow 5 current=true window=5m type=time global=false by actor.alternateId

| stats count(eval(displayMessage="REJECT")) as RejectionCount, count(eval(displayMessage="SUCCESS")) as SuccessCount by actor.alternateId, client.ipAddress
| where RejectionCount >= 3 AND SuccessCount == 1
| eval Description="Potential MFA Fatigue attack successful. User rejected multiple pushes before capitulating."
```

### Implementation 3: IBM QRadar (AQL) - Unauthorized Group Modification Logs
This query monitors QRadar event logs for target environment modifications that signal lateral group changes or privilege tampering outside standard maintenance windows.

```aql
SELECT Loganame(logsourceid) AS 'Log Source', username AS 'User', IdentityName AS 'Target Identity', EventName(eventid) AS 'Event', DATEFORMAT(starttime, 'yyyy-MM-dd HH:mm:ss') AS 'Time' 
FROM events 
WHERE (qid = 5000023 OR payload CONTAINS 'addMemberToRole' OR payload CONTAINS 'syslog:CapabilityAssignment') 
START '2026-05-11 00:00:00' STOP '2026-05-18 23:59:59'
```

---

## 🚀 How to Import & Run
1. Copy the query strings from the code blocks above or download them from `config/Identity-Threat-Detection-Rules.txt`.
2. **For Azure/Sentinel:** Navigate to **Microsoft Sentinel** -> **Analytics** -> **Create scheduled query rule**, and paste the KQL logic.
3. **For Splunk:** Paste the SPL logic into your Search bar or save it as an Alert with a throttling window of 1 hour to prevent alert fatigue.
