# 🛡️ Access Certifications: Starting a Campaign from Search

## 📋 Business Scenario & Objective
Under **SOC 2** and **ISO 27001** audit frameworks, the organization is required to perform targeted, high-frequency reviews of administrative and privileged access. 

Relying on massive, organization-wide access reviews causes **reviewer fatigue**, leading to "rubber-stamping" and unauthorized access going unnoticed. 

**Solution:** I utilized the **SailPoint IdentityNow (Identity Security Cloud) Search interface** to build laser-targeted queries that pinpoint high-risk identities, and immediately initiated targeted **Certification Campaigns** from the search results.

---

## 🛠️ Implementation Steps

### 1. Constructing the Search Query
Using SailPoint's Elasticsearch-based query syntax, I isolated high-risk accounts. To target identities that are inactive in the HR system but still possess high-level active directory privileges, I used the following query:

```text
attributes.cloudLifecycleState:inactive AND access.entitlements.name:"Domain Admins"
```

### 2. Validating the Results
* Navigated to the **Search** interface in IdentityNow.
* Executed the query and reviewed the resulting list of identities to ensure no false positives.
* Verified that the `cloudLifecycleState` mapped correctly to active AD accounts.

### 3. Launching the Campaign
* Clicked the **Certify** button directly from the search results page.
* Configured the Campaign settings:
  * **Name:** `Quarterly Inactive Privileged User Access Review`
  * **Reviewer Assignment:** Assigned to the **Manager** of each identity for initial sign-off.
  * **Deadline:** 7 days with automatic reminder notifications.
  * **Revocation Behavior:** Enabled **Automatic Revocation** upon manager denial.

---

## 📉 Business Value & Risk Reduction
* **Zero Trust Alignment:** Enforced the principle of **Least Privilege** by automating the removal of orphaned standing privileges.
* **Audit Readiness:** Provided clear, immutable audit logs showing exactly when managers reviewed and revoked access.
* **Reduced Reviewer Fatigue:** Narrowed the scope from 5,000+ entitlements down to the top 25 high-risk identities, increasing review accuracy.
