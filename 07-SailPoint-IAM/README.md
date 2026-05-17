# 🧬 SailPoint Identity Governance Engineering (IIQ & Identity Security Cloud)

This section demonstrates technical competency in managing Joiner-Mover-Leaver (JML) automation, system integration pipelines, and data normalization across hybrid SailPoint deployment architectures.

---

## 🏗️ Technical Architecture & Ecosystem Mapping

Enterprise Identity Governance and Administration (IGA) relies on clear mapping protocols to bridge the gap between human HR records and down-stream system targets.


### Business Logic Mapping Across Deployments
*   **Joiner Flow (Establish Identity):** Triggers user account data onboarding from an authoritative source.
*   **Schema & Correlation Handshake:** Automatically stitches target application accounts (e.g., Active Directory) to a central human identity profile, preventing orphan accounts.
*   **Data Aggregation Engine:** Queries end-point targets to pull entitlement structures into the governance ledger for lifecycle management and access certification.

---

## 🛠️ Project Implementation Case Study

### Objective
Standardize incoming unstructured user account data profiles from an upstream HR system source payload. The deployment target application requires capitalization standardization (converting string targets from `FINANCE AND OPERATIONS` to `Finance And Operations`).

### Implementation 1: IdentityIQ (Legacy On-Prem / Java-BeanShell)
Deployed inside an XML application data model target structure using native `sailpoint.object` classes. This script executes a loop modification pattern over raw text arrays during aggregation runtime.

```xml
<?xml version='1.0' encoding='UTF-8'?>
<!DOCTYPE Rule PUBLIC "sailpoint.dtd" "sailpoint.dtd">
<Rule name="Format Department Rule" type="ResourceObjectCustomization">
  <Description>Standardizes department attribute formatting inside IdentityIQ.</Description>
  <Source>
    <![CDATA[
      String hrDept = object.getAttribute("department"); 
      if (hrDept != null) {
          hrDept = hrDept.toLowerCase();
          String[] words = hrDept.split(" ");
          StringBuilder formattedDept = new StringBuilder();
          for (String word : words) {
              if (word.length() > 0) {
                  formattedDept.append(Character.toUpperCase(word.charAt(0))).append(word.substring(1)).append(" ");
              }
          }
          object.put("department", formattedDept.toString().trim());
      }
    ]]>
  </Source>
</Rule>
```

### Implementation 2: Identity Security Cloud (Modern Cloud / JSON API)
Deployed declaratively via the SailPoint Identity Security Cloud Rest engine using **Postman** orchestration pipelines and **VS Code**. Avoids procedural scripting runtime loops in favor of a declarative functional mapping syntax block.

*   **API Configuration Pipeline Target:** `POST https://{tenant}://`
*   **Integration Payload:**

```json
{
    "name": "Format Department Name",
    "type": "title",
    "attributes": {
        "input": {
            "type": "accountAttribute",
            "attributes": {
                "sourceName": "HR Application Source",
                "attributeName": "department"
            }
        }
    }
}
```

---

## 📈 Core Technical Competencies Highlighted
*   **Development Framework Mapping:** Proficient with data structuring syntax models including JSON schema manipulation, Apache Velocity templates, and legacy Java BeanShell context scripting wrappers.
*   **API Automation Orchestration:** Experienced utilizing Postman testing environments to pull cloud platform authentication tokens, query endpoints, and deploy core configuration transformations.
*   **Troubleshooting Architecture:** Capable of diagnosing platform authentication faults, adjusting broken correlation rules, parsing raw API response payloads, and rectifying system account anomalies.
