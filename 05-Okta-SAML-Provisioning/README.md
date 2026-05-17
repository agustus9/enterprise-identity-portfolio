# 🔑 Okta Workforce Identity & SAML Federation

## 📋 Scenario & Business Problem
An enterprise is adopting Salesforce as its primary CRM, but managing standalone user accounts directly in Salesforce creates a massive operational burden and a clear security risk when employees leave the company. 

The business requires a centralized cloud architecture where Okta acts as the identity provider (IdP). Users must authenticate once via Okta using corporate credentials and gain secure, immediate access to Salesforce via SAML 2.0 without re-entering passwords. Furthermore, when a user is assigned to a specific group in Okta, their account must be provisioned instantly in Salesforce via the SCIM protocol.

## 💡 Solution Architecture
This project models an enterprise-ready Okta-to-Salesforce application integration layout.
1. **SAML 2.0 Single Sign-On:** Establishes trusted cryptographic handshakes between Okta (Identity Provider) and Salesforce (Service Provider).
2. **Dynamic Group Membership Rules:** Evaluates user profile metadata (e.g., `department == "Sales"`) to automatically place identities into targeted Okta security groups.
3. **Automated Lifecycle Management (LCM):** Utilizes outbound SCIM APIs to push account creation, updates, and absolute access suspension (deprovisioning) downstream.
4. **Attribute Transformation:** Maps corporate Okta user profiles directly to corresponding target application application attributes.

## 🏗️ Technical Components
* **Component Type:** Okta Application Integration Configuration Metadata
* **Federation Standard:** SAML 2.0 (Assertion, EntityID, ACS URL)
* **Provisioning Engine:** SCIM v2.0 Protocol Compliance
* **Access Rules:** Expression-driven dynamic group assignments

## 🚀 How to Import & Review
1. Review the structural application integration blueprint defined in `config/Okta-SAML-Blueprint.json`.
2. To test in an Okta Preview environment, utilize the Okta Application Integration Wizard (AIW) to declare a new SAML 2.0 application.
3. Upload the public certificates to your target Service Provider (SP) configuration module to finalize the cryptographic validation trust loop.
