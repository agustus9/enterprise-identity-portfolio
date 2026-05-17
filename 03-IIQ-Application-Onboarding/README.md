# 🔌 Application Onboarding & Direct Connectors

## 📋 Scenario & Business Problem
An enterprise needs to integrate a critical Active Directory (AD) target system into SailPoint IdentityIQ. The system contains thousands of user accounts and security groups that grant access to financial file shares. 

Without onboarding this application, IT security teams have no visibility into who owns which AD accounts, creating massive compliance blind spots. The objective is to establish a secure connection, map identity schemas accurately, and configure rule-based correlation to match AD profiles with master Identity Cubes automatically.

## 💡 Solution Architecture
This project establishes a native **Active Directory Direct Connector Configuration XML**.
1. **Connectivity:** Defines secure communication channels utilizing LDAP over SSL (LDAPS) to domain controllers.
2. **Schema Mapping:** Standardizes the mapping of raw target system variables (e.g., `sAMAccountName`, `memberOf`) into default IdentityIQ account attributes.
3. **Entitlement Discovery:** Configures structured aggregation tasks to pull Active Directory Security Groups as unique manageable entitlements.
4. **Correlation Logic:** Implements fallback logic to link accounts based on matching email addresses or employee numbers.

## 🏗️ Technical Components
* **Connector Type:** Active Directory Direct Connector
* **Configuration Layers:** Account Schema, Group Schema, Task Definitions
* **Key XML Elements:** `<Application>`, `<Schema>`, `<Attributes>`

## 🚀 How to Deploy via IIQ Console
1. Copy the `Active-Directory-Connector.xml` file from this directory.
2. Log into the **SailPoint IIQ Debug Page** (`/debug`).
3. Under the Object selection dropdown, choose **Import** and upload this configuration file.
4. Open the IdentityIQ UI, navigate to **Applications** -> **Application Definition**, and input your environment's specific Domain Controller credentials and root DN paths.
