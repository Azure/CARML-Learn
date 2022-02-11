In this lab, you will 
- Update the template you created in Lab 1 to leverage the modules you published to the ACR instead and
- Learn how to extend the template with new solutions

### _Navigation_
- [Step 1 - Modify the workload file to use Bicep Registry](#Step-1---Modify-the-workload-file-to-use-Bicep-Registry)
- [Step 2 - Add new resources](#Step-2---A#dd-new-resources)
- [Step 3 - Add Diagnostic settings and RBAC](#Step-3---Add-Diagnostic-settings-and-RBAC)
---

# Step 1 - Modify the workload file to use Bicep Registry

In the previous lab you published all the necessary modules to bicep registry. You will now use them to (re-)deploy the workload you deployed in LAB 1.

1. Go to the azure portal and navigate to the `artifacts-rg` resource group and navigate to the container registry you specified in [Lab 2](/docs/wiki/Lab%202%20-%20Setup%20CARML%20prerequisites#--set-the-container-registry-unique-name).

   <img src="./media/Lab8/acrSelect.png" alt="Registry select" height="500">

1. Click on the Container registry resource and go to the `Repositories` list

   <img src="./media/Lab8/repoSelect.png" alt="Repository select" height="450">

1. Select the `bicep/modules/microsoft.resources.resourcegroups` reference and the latest version in the list

   <img src="./media/Lab8/versionSelect.png" alt="Version select" height="350">

1. Finally, copy the You will need to take note of the `Artifact reference` field

    <img src="./media/Lab8/versionRefCopy.png" alt="Version reference" height="250">

1. Repeat the previous step for all of the following resource types:
    | Name | Reference |
    | - | - |
    | Resource group | `bicep/modules/microsoft.resources.resourcegroups`
    | Storage account | `bicep/modules/microsoft.storage.storageaccounts` |
    | Log Analytics workspace | `bicep/modules/microsoft.operationalinsights.workspaces` |
    | Application insights | `bicep/modules/microsoft.insights.components` |
    | Key Vault | `bicep/modules/microsoft.keyvault.vaults` |
    | Machine Learning service | `bicep/modules/microsoft.machinelearningservices.workspaces` |

1. Open the `deploy.bicep` from Lab 1. In this file you still have the module reference set to the local file.

    <img src="./media/Lab8/localRef.png" alt="Local reference" height="300">

1. Replace each module's local reference the sequence:

        `br:` + `<the matching reference you noted down>` 
     
     For example, the resource group's first line will look similar to:

    ```bicep
    module kv 'br:<YourRegistry>.azurecr.io/bicep/modules/microsoft.resources.resourcegroups:<YourVersion>' = {
    ```

1. Eventually, all references should look similar to:

    ```bicep
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

You will now modify the template to deploy a machine learning service. In this step you will use resources that have dependencies on others, demonstrating how easy it is to reference resources created by modules.

1. Machine Learning resource requires a reference to an AppInsights instance. Add the creation of this resource. The code you need to add will be similar to the following:

    ```bicep
    module apin 'br:<<your reference to the app inisights module>>' = {
        scope: resourceGroup(resourceGroupName)
        name: 'workload-apinsights'
        params: {
            appInsightsWorkspaceResourceId: la.outputs.resourceId
            name: 'carml-lab-app-insights'
        }
    }
    ```

    > You will notice we didn't add `dependsOn`. This is not required, as Bicep automatically infer the dependency since you're using another resource property (`la.outputs.resourceId`).

1. You can now add the actual Machine Learning module. The code you need to add will be similar to the following:

    ```bicep
    module ml 'br:<<your reference to the the machine learning module>>' = {
        scope: resourceGroup(resourceGroupName)
        name: 'workload-ml'
        params: {
            associatedApplicationInsightsResourceId: apin.outputs.resourceId
            associatedKeyVaultResourceId: kv.outputs.resourceId
            associatedStorageAccountResourceId: sa.outputs.resourceId
            name: 'carml-lab-ml'
            sku: 'Basic'
            systemAssignedIdentity: true
        }
    }
    ```
## Step 3 - Add Diagnostic settings and RBAC

You can add to the solution new fancy stuff.

1. Add diagnostic settings to resources. Add the following code to the machine learning properties:

    ```bicep
    diagnosticWorkspaceId: la.outputs.resourceId
    diagnosticLogsRetentionInDays: 2
    ```

1. Add Reader role RBAC to one of the resources. You will need an object ID of a principal on your directory. You can use the one of the service principal you're using to deploy, or get the object ID from Azure Active Directory.

    <img src="./media/Lab8/object-id.png" alt="Object ID" height="400">

1. Now add the following lines of code in the properties of a resource (e.g. the storage account):

    ```bicep
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
