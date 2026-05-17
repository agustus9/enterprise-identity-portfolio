# 🔍 SIEM Engineering & Advanced Threat Hunting

## 📋 Scenario & Business Problem
Identity ecosystems are a primary target for advanced persistent threats (APTs). A common attack vector involves compromising an administrative identity to quietly modify critical platform roles, or executing a "MFA fatigue" attack to bypass access controls. 

Without specialized security monitoring, these identity security incidents blend into normal audit logs. Security Operations Centers (SOC) require optimized correlation logic to instantly flag suspicious behavior, such as a user triggering an uncommon administrative role modification in SailPoint IIQ immediately followed by a high-risk cloud login from an anomalous geolocation in Entra ID.

## 💡 Solution Architecture
This project establishes high-fidelity detection rules written in **Kusto Query Language (KQL)** and **Splunk Search Processing Language (SPL)**.
1. **Behavioral Analytics:** Baselines normal administrative behavior to isolate anomalies.
2. **Multi-Source Correlation:** Bridges on-premises SailPoint IdentityIQ audit trails with cloud-native Entra ID and Okta sign-in logs.
3. **MFA Fatigue Detection:** Tracks rapid sequences of denied push notifications followed by a successful login.
4. **Privilege Escalation Alerting:** Moniters the specific database tables or application logs tracking target group modifications.

## 🏗️ Technical Components
* **Component Type:** SIEM Detection Rules & Correlation Logic
* **Query Standards:** Microsoft Sentinel KQL, Splunk SPL
* **Log Sources Evaluated:** SailPoint IIQ AuditLogs, MicrosoftEntraID SignInLogs, Okta Syslog

## 🚀 How to Import & Run
1. Copy the query strings from `config/Identity-Threat-Detection-Rules.txt`.
2. **For Azure/Sentinel:** Navigate to **Microsoft Sentinel** -> **Analytics** -> **Create scheduled query rule**, and paste the KQL logic.
3. **For Splunk:** Paste the SPL logic into your Search bar or save it as an Alert with a throttling window of 1 hour to prevent alert fatigue.
