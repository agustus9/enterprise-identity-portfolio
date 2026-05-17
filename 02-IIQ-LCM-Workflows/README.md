# ⚙️ Lifecycle Manager (LCM) & Business Workflows

## 📋 Scenario & Business Problem
When an employee transitions to a new department (a "Mover" event), their existing business roles are often left intact, leading to "privilege creep." 

The business requires a automated lifecycle governance process: when a department change is detected, SailPoint IIQ must automatically trigger an approval work item to the **New Manager**. Access provisioning to the new department's target systems must remain pending until the manager explicitly approves or denies the request.

## 💡 Solution Architecture
This project configures an enterprise-grade **SailPoint IdentityIQ Business Process (Workflow) XML**.
1. **Trigger:** The workflow is invoked by an IdentityIQ Lifecycle Event (Mover).
2. **Analysis:** It evaluates the identity's old department versus the new department attributes.
3. **Approval Routing:** It constructs an dynamic approval step, programmatically setting the `owner` to the identity's manager.
4. **Provisioning Execution:** Upon approval, it hands a compilation of provisioning plans to the internal `Provisioner` class to modify target system accounts.

## 🏗️ Technical Components
* **Component Type:** Business Process (Workflow XML)
* **Sub-Systems utilized:** Lifecycle Manager (LCM), Workitems Engine, Provisioning Engine
* **Key Configuration Constructs:** `<Step>`, `<Transition>`, `<Approval>`

## 🚀 How to Deploy via IIQ Console
1. Copy the `Custom-Mover-Workflow.xml` file from this directory.
2. Open the **SailPoint IIQ Debug Page** (`/debug`).
3. Select **Import** from the object drop-down, upload this XML, and save.
4. Link this workflow to your global Lifecycle Event configuration under Lifecycle Manager settings.
