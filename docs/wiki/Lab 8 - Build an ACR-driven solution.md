In this lab, you will 
- Update the template you created in Lab 1 to leverage the modules you published to the ACR instead and
- Learn how to extend the template with new solutions

### _Navigation_
- [Step 1 - Modify the workload file to use Bicep Registry](#Step-1---Modify-the-workload-file-to-use-Bicep-Registry)
- [Step 2 - Add new resources](#Step-2---A#dd-new-resources)
- [Step 3 - Add Diagnostic settings and RBAC](#Step-3---Add-Diagnostic-settings-and-RBAC)
---

# Step 1 - Modify the workload file to use Bicep Registry

In the previous lab you published all the necessary modules to Bicep registry. You will now use them to (re-)deploy the workload you deployed in LAB 1.

1. Go to the azure portal and navigate to the `artifacts-rg` resource group and navigate to the container registry you specified in [Lab 2](/docs/wiki/Lab%202%20-%20Setup%20CARML%20prerequisites#--set-the-container-registry-unique-name).

   <img src="./media/Lab8/acrSelect.png" alt="Registry select" height="500">

1. Click on the Container registry resource and go to the `Repositories` list

   <img src="./media/Lab8/repoSelect.png" alt="Repository select" height="450">

1. Select the `Bicep/modules/microsoft.resources.resourcegroups` reference and the latest version in the list

   <img src="./media/Lab8/versionSelect.png" alt="Version select" height="350">

1. Finally, copy the You will need to take note of the `Artifact reference` field

    <img src="./media/Lab8/versionRefCopy.png" alt="Version reference" height="250">

1. Repeat the previous step for all of the following resource types:
    | Name | Reference |
    | - | - |
    | Resource group | `Bicep/modules/microsoft.resources.resourcegroups`
    | Storage account | `Bicep/modules/microsoft.storage.storageaccounts` |
    | Log Analytics workspace | `Bicep/modules/microsoft.operationalinsights.workspaces` |
    | Application insights | `Bicep/modules/microsoft.insights.components` |
    | Key Vault | `Bicep/modules/microsoft.keyvault.vaults` |
    | Machine Learning service | `Bicep/modules/microsoft.machinelearningservices.workspaces` |

1. Open the `deploy.Bicep` from Lab 1. In this file you still have the module reference set to the local file.

    <img src="./media/Lab8/localRef.png" alt="Local reference" height="300">

1. Replace each module's local reference the sequence:

        `br:` + `<the matching reference you noted down>` 
     
     For example, the resource group's first line will look similar to:

    ```Bicep
    module kv 'br:<YourRegistry>.azurecr.io/bicep/modules/microsoft.resources.resourcegroups:<YourVersion>' = {
    ```

1. Eventually, all references should look similar to:

    ```Bicep
    module rg 'br:<YourRegistry>.azurecr.io/bicep/modules/microsoft.resources.resourcegroups:<YourVersion>' = {}
    module sa 'br:<YourRegistry>.azurecr.io/bicep/modules/microsoft.storage.storageaccounts:<YourVersion>' = {}
    module kv 'br:<YourRegistry>.azurecr.io/bicep/modules/microsoft.keyvault.vaults:<YourVersion>' = {}
    module la 'br:<YourRegistry>.azurecr.io/bicep/modules/microsoft.operationalinsights.workspaces:<YourVersion>' = {}
    ```

2. Now, re-run the template's deployment like you did in  [Lab 1](./Lab%201%20-%20Use%20CARML%20to%20deploy%20infrastructure##--step-4---stretch-goal-deploy-solution). The deployment should succeed (even if no changes will happen).

    ```Powershell
    $inputObject = @{
        DeploymentName     = "CARML-workload-$(-join (Get-Date -Format 'yyyyMMddTHHMMssffffZ')[0..63])"
        TemplateFile       = '<FullPathToYourTemplateFile>' # Get the path via a right-click on the template file in VSCode & select 'Copy Path'
        Location           = '<LocationOfYourChoice>' # E.g. WestEurope
        Verbose            = $true
        ResourceGroupName  = '<NameOfTheResourceGroup>' # E.g. workload-rg
        StorageAccountName = '<NameOfTheStorageAccount>' # Must be globally unique
        KeyVaultName       = '<NameOfTheKeyVault>' # Must be globally unique
        LogAnalyticsName   = '<NameOfTheLogAnalyticsWorkspace>' # E.g. carml-law
    }
    New-AzSubscriptionDeployment @inputObject
    ```

# Step 2 - Add new resources

You will now modify the template to deploy a machine learning service. In this step you will 
- Use resources that have dependencies on others, demonstrating how easy it is to reference resources created by modules
- Add additional resources from your private Bicep registry and see how easy it can be to interact with it

1. Machine Learning resource requires a reference to an AppInsights instance. To get started, first at its reference the same way you did before based on the notes you copied from the registry:

    ```Bicep
    module appi 'br:<YourRegistry>.azurecr.io/bicep/modules/microsoft.insights.components:<YourVersion>' =
    ```

1. Next, select the area right behind the `=` character and press `space`. This should open a context menu that, as in Lab 1 for the local path, offers you to auto-populate the module with all required parameters.

    <img src="./media/Lab8/requiredProperties.png" alt="Required properties" height="150">

1. As you can see, application insights requires two parameters, `name` and `workspaceResourceId`. Please enter the details as follows
   - Add a `applicationInsightsWorkspaceName` parameter to the parameters section
   - Fill in the module properties as you did for the other resources (i.e. scope, deployment name). For the `workspaceResourceId` use the `resourceId` output of the `la`-module deployment. 
  
    The result should look similar to:

    ```Bicep
    // Params section
    @description('Required. The name of the log analytics workspace to deploy')
    param applicationInsightsWorkspaceName string

    // Deployments section
    module appi 'br:<YourRegistry>.azurecr.io/bicep/modules/microsoft.insights.components:<YourVersion>' = {
        scope: resourceGroup(resourceGroupName)
        name: 'workload-appi'
        params: {
            workspaceResourceId: la.outputs.resourceId
            name: applicationInsightsWorkspaceName
        }
    }
    ```

    > You may notice we didn't ask you to add a dependency to the `la` module deployment. This is not required, as Bicep creates an implicit dependency since you're using the `la`-module's output `la.outputs.resourceId`.

1. Finally, you can now add the 'Machine Learning' module. As before, we'd ask you to 
    - insert the registry-reference, 
    - generate the required parameters
    - add a parameter for the workspace `name`
    - Add a reference to the corresponding  `associated` parameters (`appi`,`kv`, `sa`)
    - set the SKU to `Basic`
 
    ```bicep
    // Params section
    param machineLearningWorkspaceName (...)

    // Deployments section
    module ml 'br:<YourRegistry>.azurecr.io/bicep/modules/microsoft.machinelearningservices.workspaces:<YourVersion>' = {
        scope: 
        name: 
        params: {
            associatedApplicationInsightsResourceId: 
            associatedKeyVaultResourceId: 
            associatedStorageAccountResourceId: 
            name: 
            sku: 
        }
    }
    ```

1. Last but not least, add a new empty line in the `ml`'s `param` block. Once done, press `space` which should open a popup that shows you all available parameters for the module. If you further press `Ctrl + Space` it should also lead all the metadata for these parameters, which means it shows for example a description for each. Now, select the `systemAssignedIdentity` parameter and set it to `true`.

    <img src="./media/Lab8/extraParam.png" alt="Extra parameter" height="200">

1. The result should look similar to similar the following:

    ```Bicep
    // Params section
    @description('Required. The name of the machine learning workspace')
    param machineLearningWorkspaceName string

    // Deployments section
    module ml 'br:<YourRegistry>.azurecr.io/bicep/modules/microsoft.machinelearningservices.workspaces:<YourVersion>' = {
        scope: resourceGroup(resourceGroupName)
        name: 'workload-ml'
        params: {
            associatedApplicationInsightsResourceId: appi.outputs.resourceId
            associatedKeyVaultResourceId: kv.outputs.resourceId
            associatedStorageAccountResourceId: sa.outputs.resourceId
            name: machineLearningWorkspaceName
            sku: 'Basic'
            systemAssignedIdentity: true
        }
    }
    ```

## Step 3 - Add Diagnostic settings and RBAC

There are also a few other features you can enable easily. For example `RBAC` & `DiagnosticSettings` for your resources.

1. You can add diagnostic settings to the `Machine Learning Workspace`, `Key Vault` & `Storage Account` module by adding the following line to each of their `param` blocks:

    ```Bicep
    diagnosticWorkspaceId: la.outputs.resourceId
    ```

    > ***Note:*** Optionally, you could also set additional parameter such as `logsToEnable` to specify which logs to set exactly. By default, all are enabled once a diagnostic target was specified.

    > ***Note:*** Linter may warn you that you should explicitely specify the module's `location` parameter once you added the diagnostic settings. To do so, you can just add `location: location` as well.

1. Add Reader role RBAC to one of the resources. You will need an object ID of a principal on your directory. You can use the one of the service principal you're using to deploy, or get the object ID from Azure Active Directory.

    <img src="./media/Lab8/object-id.png" alt="Object ID" height="400">

3. Now add the following lines of code in the properties of a resource (e.g. the storage account):

    ```Bicep
    roleAssignments: [
      {
        roleDefinitionIdOrName: 'Reader'
        principalIds: [
          '<an object id of a principal in your directory>'
        ]
      }
    ]
    ```

## Step 4 - Update your workload

You can now re-deploy your template to update and add the new resources.

1. (optional) You didn't add any new parameter to the template, so you can skip this part if your terminal is still open. If you closed the terminal, you can set up the input variable as you did in Lab1

    ```Powershell
    $inputObject = @{
        DeploymentName     = "CARML-workload-$(-join (Get-Date -Format 'yyyyMMddTHHMMssffffZ')[0..63])"
        TemplateFile       = '<FullPathToYourTemplateFile>' # Get the path via a right-click on the template file in VSCode & select 'Copy Path'
        Location           = '<LocationOfYourChoice>' # E.g. WestEurope
        Verbose            = $true
        ResourceGroupName  = '<NameOfTheResourceGroup>' # E.g. workload-rg
        StorageAccountName = '<NameOfTheStorageAccount>' # Must be globally unique
        KeyVaultName       = '<NameOfTheKeyVault>' # Must be globally unique
        LogAnalyticsName   = '<NameOfTheLogAnalyticsWorkspace>' # E.g. carml-law
    }
    ```

1. Now execute again the deployment

    ```Powershell
    New-AzSubscriptionDeployment @inputObject
    ```

1. At last, you can check in the Azure portal if the template deployed what you expected to.

<!-- TODO: Extend with how to use bicepconfig.json ? -->

---
---

If ready, proceed to the next lab: [Lab 9 - Interoperability](./Lab%209%20-%20Interoperability)
