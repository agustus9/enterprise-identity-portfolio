# 🛡️ Okta Workforce Identity & SAML Federation

## 📋 Business Scenario & Objective
To secure access to enterprise software-as-a-service (SaaS) applications, the organization must implement centralized **Identity Provider (IdP)** controls. 

**The Challenge:** Users managing separate, insecure passwords for applications like Salesforce, leading to access sprawl and administrative overhead.
**The Solution:** I engineered an **Okta Workforce Identity** architecture configuring secure single sign-on (SSO) via SAML 2.0 and automated provisioning via Lifecycle Management (LCM).

---

## 🛠️ Implementation & Module Builds

### 1. User & Role Management
* **User Management:** Built out comprehensive user profiles and managed direct lifecycle states (Active, Suspended, Deactivated).
* **Role Assignments:** Created explicit group-to-role mappings to automatically govern user access based on organizational structure.
* **Group Rules:** Configured **Group Membership Rules** using Okta Expression Language to automate the onboarding of specific business unit users.

### 2. SAML 2.0 Federation (SAML IdP & SP)
* **Salesforce SAML Integration:** Configured the Salesforce application with SAML 2.0 inside the **Okta Admin Portal**.
* **SP-Initiated SAML:** Configured Service Provider (SP)-Initiated SAML authentication between Salesforce and Okta to force secure, centralized user authentication.

### 3. Lifecycle Management (Provisioning & LCM)
* **Salesforce Provisioning:** Enabled automated **SCIM** provisioning from Okta to Salesforce to automatically push user profile updates downstream.
* **JML Automation (LCM):** Configured automated account suspension and revocation inside the Salesforce application whenever an identity's state changes in Okta.

---

## 📉 Business Value & Risk Reduction
* **Automated Provisioning:** Eliminated manual IT tickets for account creation and termination.
* **Centralized Authentication:** Reduced credential theft risk by removing direct Salesforce passwords and routing all traffic through Okta IdP policies.
* **Audit Compliance:** Ensured all administrative role changes and application assignments are logged and traceable for internal reviews.
