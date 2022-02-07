In order to successfully deploy and test all modules in your desired environment some modules have to have resources deployed beforehand. In this lab you will deploy the modules' dependencies by manually triggering the **dependency pipeline**.

### _Navigation_
- [Step 1 - Add a dependency](#step-1---add-a-dependency)
- [Step 2 - Upload your changes to GitHub](#step-2---upload-your-changes-to-github)
- [Step 3 - Trigger the dependency pipeline](#step-3---trigger-the-dependency-pipeline)
- [Step 4 - What you will deploy](#step-4---what-you-will-deploy)

---

# Step 1 - Add a dependency

Together with the resource modules pipelines, we are providing a dependency pipeline (GitHub workflow: `.github\workflows\platform.dependencies.yml`), leveraging resource parameters from the `utilities\dependencies` subfolder. 

As part of a later lab you will need an additional dependency, a proximity placement group, that is currently not part of the dependency pipeline. For this purpose, perform the following tasks to add the new dependency.

1. In your local VSCode, navigate to the dependencies compute folder

    <img src="./media/Lab3/dependencyCompute.png" alt="Compute folder" height="250">

1. Add a new folder `proximityPlacementGroups`

    <img src="./media/Lab3/depNewFolder.png" alt="PPG folder" height="80">

2. Add a new parameter file `parameters.json` to the created folder. This folder hosts the minimum parameters you need to deploy the dependency.

    <img src="./media/Lab3/depNewFile.png" alt="PPG File" height="95">

3. Add the below snippet to the file

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "name": {
                "value": "adp-<<namePrefix>>-az-ppg-x-001"
            }
        }
    }
    ```

4. Navigate to the dependency pipeline in path `.github\workflows\platform.dependencies.yml`

    <img src="./media/Lab3/dependenciesWorkflow.png" alt="Dependencies workflow" height="220">

5. Add the below snippet in between the `job_deploy_rg` & `job_deploy_msi` job in line 62. You can add spaces as you see fit. 
   > ***Note:*** Make sure the snipped uses the same ident as the other jobs as YAML is space-sensitive
   > ***Note:*** If this would be a contribution to the main CARML repository, we would ask you to perform the same update to the Azure DevOps dependency pipeline  

    ```Yaml
    job_deploy_ppg:
      runs-on: ubuntu-20.04
      name: 'Deploy proximity placement group'
      env:
        namespace: 'Microsoft.Compute\proximityPlacementGroups'
      needs:
        - job_deploy_rg
      strategy:
        fail-fast: false
        matrix:
          parameterFilePaths: ['parameters.json']
      steps:
        - name: 'Checkout'
          uses: actions/checkout@v2
          with:
            fetch-depth: 0
        - name: 'Deploy module'
          uses: ./.github/actions/templates/validateModuleDeployment
          with:
            templateFilePath: 'arm/${{ env.namespace }}/deploy.bicep'
            parameterFilePath: '${{ env.dependencyPath }}/${{ env.namespace }}/parameters/${{ matrix.parameterFilePaths }}'
            location: '${{ env.defaultLocation }}'
            resourceGroupName: '${{ env.defaultResourceGroupName }}'
            subscriptionId: '${{ secrets.ARM_SUBSCRIPTION_ID }}'
            managementGroupId: '${{ secrets.ARM_MGMTGROUP_ID }}'
            removeDeployment: '${{ env.removeDeployment }}'
   ``` 

# Step 2 - Upload your changes to GitHub

Now that the new dependency is implemented, you can upload your changes to GitHub.

1. This can either be done via the terminal or by using the Git integration of VSCode. To keep it simple, this lab assumes you want to use the VSCode's git integration. If not already there, navigate to the source control menu to the left.

    <img src="./media/Lab3/gitInit.png" alt="Open source control" height="120">

1. Here you first need to add the changed files to the commit. To do so, select the `+` icon next to `Changes` (appears when hovering)

    <img src="./media/Lab3/gitAdd.png" alt="Git add" height="120">

1. Next, you should give the commit a meaningful message such as 'Added Proximity Placement Group to dependency pipeline' and can then click the checkmark symbol on the top to create the commit

    <img src="./media/Lab3/gitCommit.png" alt="Git commit" height="140">

1. Finally, you can push the changes to the repository by selecting the blue `Publish Branch` button

    <img src="./media/Lab9/gitpush.png" alt="Git push" height="80">

# Step 3 - Trigger the dependency pipeline

Usually, the dependency workflow would not run frequently, but only when new module dependencies are identified or its original state must be recovered. Therefore, the type of trigger that has been configured is the `workflow_dispatch`, which allows us to manually trigger the workflow when needed.

1. In the repository menu open the `Actions` tab.

    <img src="./media/Lab5/openActions.png" height="180">

1. From the workflows list, select the one named `.Platform: Dependencies`.
    
    <img src="./media/Lab3/dependey_workflow.png" height="250">

1. To the right, you will see a `Run workflow` button. Click on it, select your branch from the branch dropdown menu and trigger the workflow by clicking on the green button `Run workflow`.

    <img src="./media/Lab3/dependencyTrigger.png" height="350">

> For the sake of saving time in the lab, we will leave the SQL Managed Identity and VHD check boxes **unchecked**.

1. After running the workflow, if you wait a few seconds or refresh the page, you will see that a new execution is in progress.

    <img src="./media/Lab3/execution.png" height="100">

1. You will verify that the workflow has finished once the status is green.

    <img src="./media/Lab3/workflow_finished.png" height="300">


> This workflow may take up to 20 minutes to execute. You can proceed with the following steps and labs and revisit the pipeline later to make sure it succeeded.

# Step 4 - What you will deploy

In order to successfully deploy and test all modules in your desired environment some modules have to have resources deployed beforehand. Since also dependency resources are in turn subject to dependencies with each other, resources are deployed in the following grouped order.

**First level resources**: resource groups leveraged by all modules. Multiple instances are deployed:
- `validation-rg`: The resource group to which resources are deployed by default during the test deployment phase. This same resource group is also the one hosting the dependencies.
- `artifacts-rg`: The resource group to which templates are published during the publishing phase.

**Second level resources**: This group of resources has a dependency only on the resource group which will host them. Resources in this category include a log analytics workspace, storage account, event hub namespace, Azure container registry, etc.
> ***Note:*** This group contains the previously added Proximity Placement group

**Third level resources**: This group of resources has a dependency on one or more resources in the group above. In this group we can find a key vault, recovery services vault, application insights, etc.

**Fourth level resources**: This group of resources has a dependency on one or more resources in the groups above. These resources include some virtual networks and a AVD application group.

Finally, the **fifth level resources** are resources with dependency on all the other groups. They include a virtual machine and a private DNS zone.


<img src="./media/Lab3/dependencies_groups.png" height="330">

--> [Now proceed to the next LAB](./Lab%204%20-%20First%20pipeline%20run) -->
