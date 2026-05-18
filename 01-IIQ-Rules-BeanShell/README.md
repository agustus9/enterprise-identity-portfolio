# 🛠️ Custom Rule Engineering & BeanShell Scripts

## 📋 Scenario & Business Problem
During an enterprise application onboarding phase, the target HR/Database application does not provide a standard unique identifier (like an Employee ID) in its account feed. Instead, accounts contain a messy string representing full names and a department code (e.g., `Smith, John (FIN)`). 

Without a direct correlation key, SailPoint IdentityIQ cannot automatically link these target application accounts to the master **Identity Cubes**, resulting in orphan accounts and unmanaged access risks.

## 💡 Solution Architecture
This project implements a custom **SailPoint IIQ Correlation Rule** written in Java/BeanShell. 
1. **Extraction:** The rule parses the incoming messy attribute string (`manager_string`) using standard regular expressions.
2. **Database Lookup:** It extracts the clean first name, last name, and department.
3. **API Query:** It leverages the native SailPoint API (`sailpoint.api.Context` and `QueryOptions`) to query the identity repository.
4. **Correlation:** It safely returns a matching `Identity` object or returns `null` if a deterministic match cannot be found, preventing false positives.

## 🏗️ Technical Components
* **Rule Type:** Correlation Rule
* **Language Engine:** BeanShell / Java 1.8 Compliance
* **Key API Classes Used:** 
  * `sailpoint.api.Context` (Engine interface)
  * `sailpoint.object.Identity` (Identity Cube mapping)
  * `sailpoint.object.QueryOptions` & `Filter` (Efficient DB querying)

---

## 🛠️ BeanShell Script Engineering Implementation

Below is the clean, commented representation of the custom IdentityIQ Correlation Rule script that executes against the internal identity repository during aggregation tasks:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<!DOCTYPE Rule PUBLIC "sailpoint.dtd" "sailpoint.dtd">
<Rule name="Custom HR Account Correlation Rule" type="Correlation">
  <Description>
    Parses complex, unstructured account attributes from an HR feed and matches them deterministically to an active Identity Cube.
  </Description>
  <Source>
     1) {
                logger.warn("Ambiguous data: Multiple identities match the pattern. Skipping auto-correlation to prevent security collision.");
            } else {
                logger.warn("No active identity matching " + firstName + " " + lastName + " found in department " + deptCode);
            }
        } else {
            logger.error("Failed to parse raw data string layout: " + rawData);
        }
    }

    // 6. Return map containing the correlated identity name to the provisioning engine
    Map returnMap = new HashMap();
    if (matchedIdentity != null) {
        returnMap.put("identityName", matchedIdentity.getName());
    } else {
        returnMap.put("identityName", null); // Places account in the 'Uncorrelated' state for manual review
    }

    return returnMap;
    ]]>
  </Source>
</Rule>
```

---

## 🚀 How to Deploy via IIQ Console
1. Copy the `Custom-Correlation-Rule.xml` file from this directory or extract the XML script block above.
2. Log into the **SailPoint IIQ Console** or navigate to the Global Settings UI (`Debug Page`).
3. Import the XML file into the environment.
4. Go to your Application configuration page, select the **Correlation** tab, and assign this rule to the Correlation configuration.
