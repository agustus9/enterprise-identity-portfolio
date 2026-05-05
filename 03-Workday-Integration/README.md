# 🏢 Workday HR-Driven Lifecycle Automation (JML)

## 📋 Business Scenario & Objective
Manually managing user identities during onboarding, department transfers, and offboarding introduces high operational overhead and poses significant security risks, such as orphaned accounts and privilege creep.

**The Challenge:** Connecting the HR source of truth directly to the access enforcement layer without manual IT tickets or intervention.
**The Solution:** I engineered a **Workday-to-SailPoint IdentityNow** integration that consumes HR events and automatically drives **Joiner-Mover-Leaver (JML)** workflows to enforce birthright access and zero-day terminations.

---

## 🛠️ Implementation & Architecture

```text
[ Workday HR Event ] ──> [ SailPoint IDN API / Source ] ──> [ Identity Profile / Lifecycle States ]
                                                                       │
                                      ┌────────────────────────────────┴────────────────────────────────┐
                                      ▼                                                                 ▼
                        [ Trigger: Joiner / Mover ]                                      [ Trigger: Leaver (Termination) ]
                                      │                                                                 │
                                      ▼                                                                 ▼
                       [ Provision Birthright Access ]                                    [ Immediate Account Disabling ]
```

### 1. Connecting the Authoritative Source
* **Source Configuration:** Created the Workday HR source in **IdentityNow** via the native Workday connector.
* **API Integration:** Configured basic authentication and OAuth 2.0 connection parameters to ingest worker records securely.
* **Aggregation Schema:** Selected and mapped core Workday attributes:
  * `Worker_ID` $\longrightarrow$ Maps to the unique internal identifier.
  * `Employment_Status` $\longrightarrow$ Drives the lifecycle state evaluation.
  * `Business_Title` & `Cost_Center` $\longrightarrow$ Utilized for role-based access assignment.

### 2. Lifecyle State Configuration (JML)
I mapped Workday's status changes directly to the **Identity Profile Lifecycle States** in SailPoint to automate downstream account states:


| Workday Trigger | SailPoint Lifecycle State | Provisioning Action |
| :--- | :--- | :--- |
| `Pre-Hire / Active` | **Joiner (Active)** | Automates the creation of AD, Entra ID, and Okta accounts. Provisions basic "Birthright" application access. |
| `Position Change / Promotion` | **Mover (Active)** | Triggers an immediate recalculation of Role Membership rules; revokes legacy department access and grants new role entitlements. |
| `Terminated / Resigned` | **Leaver (Inactive)** | Triggers **Zero-Day Revocation**. Disables downstream Active Directory and SaaS accounts instantly; strips all enterprise entitlements. |

### 3. Attribute Sync & Account Correlation Rules
* **Account Mapping:** Configured identity attribute transformations to auto-generate `sAMAccountName` and `mail` values from Workday inputs.
* **Correlation Rules:** Wrote custom correlation parameters to link new Workday hires directly to matching existing pre-hire accounts, preventing the creation of duplicate identities.

---

## 📉 Business Value & Risk Reduction
* **Zero-Day Termination:** Eliminated the "access gap" between an HR exit event and the disabling of system access, reducing the risk of ex-employee data theft.
* **Birthright Access Day 1:** Reduced the time-to-productivity for new hires by automating the assignment of baseline software access.
* **Continuous Compliance:** Ensured that identity lifecycle triggers are immutably logged for **SOC 2** and **ISO 27001** audits.
