# ⚙️ SailPoint IdentityNow: Workflows & Transforms

## 📋 Business Scenario & Objective
In an enterprise environment, raw data coming from authoritative sources (like Workday) is often inconsistent, leading to errors during downstream account provisioning in Active Directory.

**Solution:** I developed **JSON Transforms** to clean and standardize user attributes, and configured **IdentityNow Workflows** to trigger immediate automated responses to HR lifecycle changes.

---

## 🛠️ Part 1: Automated Data Normalization (Transforms)

### Business Problem
Raw user strings extracted from the HR system contained irregular casing and exceeded the maximum character limits required by downstream directory services.

### Implementation
I authored a custom **Lowercase and Substring Transform** that intercept the raw `firstname` attribute from the authoritative source, trims it to 8 characters, and converts it to lowercase to construct a valid, unique `uid`.

```json
{
  "name": "Normalize and Truncate UID",
  "type": "lower",
  "attributes": { ... }
}
```

---

## 🛠️ Part 2: Automated Lifecycle State Triggers (Workflows)

### Business Problem
When an employee transitions out of the company, delays in revoking access increase the risk of insider threats and orphaned account exploitation.

### The JML Automation Workflow
Using the IdentityNow **Workflow Builder**, I created an automated lifecycle workflow mapped to the `cloudLifecycleState` change trigger:

```text
[Identity Attribute Changed] ──> [Filter: State = Inactive] ──> [Action: Disable AD Account]
```

### Execution Steps
1. **Trigger:** Listened for changes on the identity attribute `cloudLifecycleState`.
2. **Operator (Logic):** Used **AND/OR** conditional routing to confirm the user's state switched from `active` to `inactive`.
3. **Action:** Executed an automated API call to disable the user's correlated Active Directory account and revoke all standing enterprise entitlements immediately.

---

## 📉 Business Value & Risk Reduction
* **Zero-Day Revocation:** Eradicated the gap between HR termination and access termination.
* **Data Consistency:** Enforced identity standard schemas across all connected target applications.
* **Operational Efficiency:** Eliminated manual intervention for account de-provisioning tickets in IT service desks.
