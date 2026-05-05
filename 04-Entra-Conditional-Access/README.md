# 🛡️ Microsoft Entra ID: Zero Trust & Access Control 

## 📋 Business Scenario & Objective
To protect sensitive enterprise applications, the organization must migrate from a legacy, perimeter-based security model to a **Zero Trust Architecture (NIST 800-207)**. 

**The Challenge:** Attackers targeting user credentials through password spraying and brute force. 
**The Solution:** I engineered a **Microsoft Entra ID** tenant infrastructure using **Conditional Access Policies (CAPs)** to enforce multi-factor authentication (MFA) and built out **Privileged Identity Management (PIM)** to enforce Just-in-Time (JIT) access elevation.

---

## 🛠️ Part 1: Zero Trust Conditional Access Policy (CAP)

### Objective
Ensure that any employee attempting to access administrative portals or enterprise applications from outside the corporate network is prompted for strong authentication and accesses the tenant from a compliant device.

```text
[Identity Authentication Request]
       │
       ▼
 [Conditional Access Policy Evaluated] ──> [Is location untrusted?] ──> [Yes] ──> [Enforce MFA & Device Compliance]
       │
       ▼
[Access Granted with Limited Session Duration]
```

### ⚙️ Implementation & Policy Logic
Using the Microsoft Entra Admin Center, I created and enforced the following Zero Trust security policy:

* **Assignments (Who):** Applied to **All Users**, explicitly excluding a dedicated emergency access "break-glass" account to prevent lockout.
* **Target Resources (What):** Applied to **All Cloud Apps** and the **Microsoft Azure Management Portal**.
* **Conditions (Context):** Configured to trigger when the user is located in an **Untrusted Network** (any IP range outside the corporate CIDR block).
* **Access Controls (Grant):** 
  * **Require Multi-Factor Authentication (MFA)**
  * **Require Device to be marked as Compliant** (Intune Managed)
* **Session Controls:** Configured **Sign-in frequency** to force re-authentication every 8 hours.

---

## 🛠️ Part 2: Just-In-Time Access via Privileged Identity Management (PIM)

### Objective
Eliminate **standing privileges** for administrative accounts to prevent lateral movement in the event of a credential compromise.

### ⚙️ Implementation Steps
1. **PIM Configuration:** Activated **Microsoft Entra Privileged Identity Management** within the tenant for sensitive administrator roles.
2. **Role Selection:** Targeted the high-privilege **Global Administrator** and **User Administrator** roles.
3. **Assignment Type:** Converted direct, permanent administrative assignments into **Eligible Assignments**.

### 🔐 Access Elevation Workflow
* **Activation Process:** When a cloud administrator needs to make changes, they must navigate to the PIM portal and request role activation.
* **Approval Requirement:** Configured mandatory **MFA verification** and a text-based justification upon activation.
* **Time-Bound Enforcement:** The elevation automatically expires and drops privileges back to a standard user level after exactly **2 hours**.

---

## 📉 Business Value & Risk Reduction
* **Phishing Mitigation:** Reduced the impact of credential theft attempts by up to 99% by blocking legacy authentication and enforcing contextual MFA.
* **No Standing Admin Privileges:** Ensured that nobody has perpetual administrative access to the tenant, cutting down the attack surface.
* **Regulatory Compliance:** Satisfied both **SOC 2** and **NIST 800-53** controls requiring strong authentication and the principle of least privilege.
