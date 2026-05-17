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

## 🚀 How to Deploy via IIQ Console
1. Copy the `Custom-Correlation-Rule.xml` file from this directory.
2. Log into the **SailPoint IIQ Console** or navigate to the Global Settings UI (`Debug Page`).
3. Import the XML file into the environment.
4. Go to your Application configuration page, select the **Correlation** tab, and assign this rule to the Correlation configuration.
