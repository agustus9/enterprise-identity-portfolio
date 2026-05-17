# 🛡️ Entra ID Zero Trust & Access Control

## 📋 Scenario & Business Problem
An organization is moving to a hybrid work model, increasing the risk of credential theft and unauthorized access to cloud resources. Left unmanaged, users can log into critical corporate infrastructure from unmanaged personal devices and high-risk locations without additional verification. 

Furthermore, IT administrators hold persistent, permanent global admin privileges, creating a massive attack surface. The business requires a Zero Trust architecture that enforces context-aware access controls and restricts administrative access to an on-demand, approved basis.

## 💡 Solution Architecture
This project implements a rigorous cloud access security baseline using Microsoft Entra ID.
1. **Adaptive Conditional Access:** Restricts cloud app access based on user location, device compliance state, and real-time sign-in risk scores.
2. **Step-Up Authentication:** Forces phishing-resistant Multi-Factor Authentication (MFA) whenever an anomalous login behavior is detected.
3. **Privileged Identity Management (PIM):** Eliminates standing administrative privileges by introducing Just-In-Time (JIT) role activation requiring multi-stage business approvals.
4. **Session Controls:** Enforces continuous access evaluation and limits session lifetimes on unmanaged or untrusted web browsers.

## 🏗️ Technical Components
* **Component Type:** Azure Resource Manager (ARM) / JSON Policy Definitions
* **Security Scopes:** Conditional Access Policies, Privileged Identity Management (PIM), Authentication Strengths
* **Core Enforcements:** Phishing-resistant MFA, Device Compliance, Managed Risk Gates

## 🚀 How to Deploy via Entra ID Portal
1. Copy the JSON policy configuration from `config/Conditional-Access-Policy.json`.
2. Navigate to the **Microsoft Entra admin center** -> **Protection** -> **Conditional Access**.
3. Select **New policy from template** or upload the JSON parameters using the Graph API / PowerShell.
4. Set the policy state to **Report-only** first to analyze business impact before switching to **On**.
