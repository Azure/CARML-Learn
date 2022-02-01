In this lab, you will learn how to use the modules that are currently available in the CARML library to build a basic, template-orchestrated solution - using local file paths.

### _Navigation_
- [Step 1 - Download the code base](#step-1---download-the-code-base)
- [Step 2 - Create solution file](#step-2---create-solution-file)
- [Step 3 - Build solution](#step-3---build-solution)
- [Step 4 - Stretch goal: Deploy solution](#step-4---stretch-goal-deploy-solution)
---

# Step 1 - Download the code base

So start building a solution / workload, you first have to get access to the code base. One of the ways to do so is to follow the following sequence of steps:

1. Open to the repository via the URL [aka.ms/CARML](https://aka.ms/CARML)

    <img src="./media/PreReqGitHub/carmlMain.png" alt="Repository main" height="240">

2. On the overview page, select the `<> Code` button to the right, and select the `Download ZIP` button in the opening pop up to trigger the repository to be downloaded as a compressed file.

    <img src="./media/Lab1%20-%20First%20Solution/downloadZip.png" alt="Download zip" height="300">

3. Following the download, navigate the downloaded file and expand it to a location of your choice (for example `C:/dev/CARML/lab1`)

    <img src="./media/Lab1%20-%20First%20Solution/../Lab1%20-%20First%20Solution/downloadedZip.png" alt="Downloaded zip" height="50">

1. Next, navigate to inside the expanded folder, perform a right-click and select `Open in Windows Terminal` to open a new PowerShell session with the path set to the current folder

    <img src="./media/PreReqGitHub/localTerminalOpen.png" alt="Open local terminal" height="230">

2. Next, just type & execute the command `code ./ResourceModules` to open Visual Studio Code in the clone repository folder folder

    <img src="./media/Lab1%20-%20First%20Solution/downloadedCARML.png" alt="CARML folder in VSCode" height="500">

# Step 2 - Create solution file

In Visual Studio, the next step is to create the solution itself. To do so, perform the following steps:

1. Create a folder called `workload` in the root folder by selecting the small `New Folder` button on the top of the file explorer in VSCode

    <img src="./media/Lab1%20-%20First%20Solution/CreateFolder.png" alt="Create Local Folder" height="95">

1. Next, select the new folder and trigger the small `New File` icon left of the `New Folder` icon and call it `deploy.bicep`. This will be the folder you create the workload in.

    <img src="./media/Lab1%20-%20First%20Solution/NewFile.png" alt="Create Local Folder" height="170">

# Step 3 - Build solution

Now, let's create the actual solution. In this first lab we will start with a small setup to get rolling.

Fundamentally, we want you to deploy
- A Resource Group
- A Storage Account
- A Key Vault
- A Log Analytics workspace

To set these up, please follow the following steps:

1. Open the created workload file in `workload/deploy.bicep`

1. As you will deploy a resource group, we first need to set the template scope to subscription. To do so, add the following line on the top of the file

   ```bicep
   targetScope = 'subscription'
   ```

1. Next, add the following parameters to the template

    ```bicep
    // ========== //
    // Parameters //
    // ========== //
 
    @description('Optional. The name of the resource group to deploy')
    param resourceGroupName string = 'validation-rg'
 
    @description('Optional. The location to deploy into')
    param location string = deployment().location
 
    @description('Optional. The name of the storage account to deploy')
    param storageAccountName string = '<AddGloballyUniqueName>'
 
    @description('Optional. The name of the key vault to deploy')
    param keyVaultName string = '<AddGloballyUniqueName>'

    @description('Optional. The name of the log analytics workspace to deploy')
    param logAnalyticsName string = 'carmllaw'
    ```

1. Now, you will add the references to the individual CARML modules you will deploy. Let's start with the resource group. Underneath the parameters, add the following block

    ```bicep
    // =========== //
    // Deployments //
    // =========== //

    module rg '../arm/Microsoft.Resources/resourceGroups/deploy.bicep' = 
    ```

   Fundamentally, the above snipped references the local path to the ResourceGroup CARML module. Thanks to Bicep's ability to resolve the reference, it should open a pop-up and ask you whether you want to auto-insert the `required parameters` (if it does not come up automatically, try to remove & add the `=`, or press `Ctrl + Space`). Press `Enter` to confirm. These parameters are the once for which the bicep module does not have default values for.

    <img src="./media/Lab1%20-%20First%20Solution/requiredProperties.png" alt="Create Local Folder" height="120">

    Once confirmed it will generate the following skeleton: 

    ```bicep
    module rg '../arm/Microsoft.Resources/resourceGroups/deploy.bicep' = {
     name: 
     params: {
         name: 
     }
    }
    ```

    As you can see, the module only requires you to provide a deployment `name` (that will be visible in the list of deployments in the portal), as well as a `name` as in its parameter block - referring to the resource group name. If you want to know what other parameters would be supported, feel free to check up on the module's readme.md that is located in the same folder as the template itself.

    By default, the resource group is deployed into the same location as the deployment. For the sake of this lab, please add an additional parameter `location` in the `params` block of the module. Here you will reference the location that is defined as an input parameter in this template's parameter block above.

    Following you can find an example of how a complete reference would look like: 

    ```bicep
    module rg '../arm/Microsoft.Resources/resourceGroups/deploy.bicep' = {
     name: 'workload-rg'
     params: {
         name: resourceGroupName
         location: location
     }
    }
    ```

    Now, please fill in the parameters accordingly.

# Step 4 - Stretch goal: Deploy solution