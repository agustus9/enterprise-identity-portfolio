# ⚙️ Lifecycle Manager (LCM) & Business Workflows

## 📋 Scenario & Business Problem
When an employee transitions to a new department (a "Mover" event), their existing business roles are often left intact, leading to "privilege creep." 

The business requires an automated lifecycle governance process: when a department change is detected, SailPoint IIQ must automatically trigger an approval work item to the **New Manager**. Access provisioning to the new department's target systems must remain pending until the manager explicitly approves or denies the request.

## 💡 Solution Architecture
This project configures an enterprise-grade **SailPoint IdentityIQ Business Process (Workflow) XML**.
1. **Trigger:** The workflow is invoked by an IdentityIQ Lifecycle Event (Mover).
2. **Analysis:** It evaluates the identity's old department versus the new department attributes.
3. **Approval Routing:** It constructs a dynamic approval step, programmatically setting the `owner` to the identity's manager.
4. **Provisioning Execution:** Upon approval, it hands a compilation of provisioning plans to the internal `Provisioner` class to modify target system accounts.

## 🏗️ Technical Components
* **Component Type:** Business Process (Workflow XML)
* **Sub-Systems Utilized:** Lifecycle Manager (LCM), Workitems Engine, Provisioning Engine
* **Key Configuration Constructs:** `<Step>`, `<Transition>`, `<Approval>`

---

## 🛠️ Workflow Engine Implementation Schema

Below is the structured representation of the custom Business Process deployment script. It tracks logic validation, dynamic manager approval routing, and conditional execution transitions.

```xml
<?xml version='1.0' encoding='UTF-8'?>
<!DOCTYPE BusinessProcess PUBLIC "sailpoint.dtd" "sailpoint.dtd">
<BusinessProcess name="Custom Mover Approval Workflow" type="LCM">
  
  <Description>
    Advanced lifecycle business process that routes manager approvals during internal employee transfers (Mover Event).
  </Description>

  <!-- Variable Definitions passed into the workflow engine -->
  
    <Description>The name of the identity undergoing the department change.</Description>
  </Variable>
  <Variable name="managerName" input="true">
    <Description>The supervisor responsible for approving access changes.</Description>
  </Variable>
  <Variable name="project" output="true">
    <Description>Compiled Provisioning Project containing the target system account plans.</Description>
  </Variable>

  <!-- STEP 1: Start and Initialization -->
  <Step name="Start" icon="Start" posX="50" posY="100">
    <Transition to="Audit Mover Event"/>
  </Step>

  <!-- STEP 2: Logic Validation -->
  <Step name="Audit Mover Event" posX="180" posY="100">
    <Script>
      <Source>
        
      </Source>
    </Script>
    <Transition to="Manager Approval Step"/>
  </Step>

  <!-- STEP 3: Route Approval Notification to Manager -->
  <Step name="Manager Approval Step" icon="Approval" posX="320" posY="100">
    <Approval name="Manager Transfer Review" owner="ref:managerName" return="project">
      
      
    </Approval>
    <!-- Dynamic transition path based on manager's action -->
    <Transition to="Execute Provisioning Plan" approved="true"/>
    <Transition to="Cancel Lifecycle Process"/>
  </Step>

  <!-- STEP 4: Trigger Access Provisioning Engine -->
  <Step name="Execute Provisioning Plan" icon="Task" posX="500" posY="50">
    <Script>
      <Source>
        <![CDATA[
        import sailpoint.api.Provisioner;
        if (project != null) {
            Provisioner p = new Provisioner(context);
            p.execute(project);
        }
        ]]>
      </Source>
    </Script>
    <Transition to="End"/>
  </Step>

  <!-- STEP 5: Rejection Path Handler -->
  <Step name="Cancel Lifecycle Process" icon="Stop" posX="500" posY="180">
    <Script>
      <Source>
        <![CDATA[
        context.decache(); // Clean context cache since request was aborted
        ]]>
      </Source>
    </Script>
    <Transition to="End"/>
  </Step>

  <!-- STEP 6: Core Exit termination -->
  <Step name="End" icon="Stop" posX="650" posY="100"/>

</BusinessProcess>
```

---

## 🚀 How to Deploy via IIQ Console
1. Copy the `Custom-Mover-Workflow.xml` file from this directory or extract the XML block above.
2. Open the **SailPoint IdentityIQ Debug Page** (`/debug`).
3. Select **Import** from the object drop-down, upload this XML, and save.
4. Link this workflow to your global Lifecycle Event configuration under Lifecycle Manager settings.
