In this lab, you will learn how a contribution to the source repository (in this example the CARML main repository) would look like. At a customer, the same approach could be used to allow teams to contribute to a central library.

### _Navigation_
- [Step 1 - Implement the contribution](#step-1---implement-the-contribution)
- [Step 2 - Run local test(s)](#step-2---run-local-tests)
- [Step 3 - Re-generate the documentation](#step-3---re-generate-the-documentation)
- [Step 4 - Re-run local test(s)](#step-4---re-run-local-tests)
- [Step 5 - Upload your changes and run the module pipeline](#step-5---upload-your-changes-and-run-the-module-pipeline)
- [Step 6 - Create a PR](#step-6---create-a-pull-request)
- [Step 7 - Exclude environment-specific changes](#step-7---exclude-environment-specific-changes)
  
---

# Step 1 - Implement the contribution
<!-- E.g. update parameter metadata -->

The first step of any contribution is its implementation. For the sake if this lab, we will suggest a simple contribution that will allow you the make use of some of our tools:

1. In your local VSCode, navigate via `arm`, on to `Microsoft.Compute` and finally to the `availabilitySets`'s template `deploy.bicep`

    <img src="./media/Lab7/asDeployBicepSelect.png" alt="Availability Set VSCode" height="300">

1. A simple contribution to perform is the addition of an additional `output` the bicep template will return upon successful execution. As we have seen, these outputs are particularly useful when multiple modules are orchestrated together. Please add the following snippet to the end of the file: 
   
    ```bicep
    @description('A list of references to all virtual machines in the availability set.')
    output virtualMachines array = availabilitySet.properties.virtualMachines
    ```

# Step 2 - Run local test(s)

CARML comes with a number of tools that you can use to perform several automated tasks for you. One of these tools can be use to perform the same tests locally as you would see in the pipeline. To do so, please perform the following tasks:

1. Navigate navigate through the folders `utilities` and `tools` and click on the script `Test-ModuleLocally.ps1`.

    <img src="./media/Lab7/TestLocalOpen.png" alt="Open Test Local script" height="250">

1. Next, you must load the function implemented in the script by triggering the `Run` button to the top right of VSCode.

    <img src="./media/Lab7/loadScriptTest.png" alt="load script for testing" height="50">

    > ***Note***: Should VSCode ask you (in the terminal) whether you are sure you want to execute the script, please confirm
    > <img src="./media/Lab7/allowsScript.png" alt="Enable execution" height="100">

2. Once confirmed, the function will be loaded and can be invoked via the command of the same name `Test-ModuleLocally`. As stated earlier, the script replicates the feeling of the pipeline. That means, it can run a simple Pester test, but also a deployment which includes a parameter file using tokens. For our purpose, please invoke the function as follows

    ```PowerShell
    $TestModuleLocallyInput = @{
        templateFilePath           = '<PathToTheUpdatedTemplate>' # Get the path via a right-click on the updated template file in VSCode & select 'Copy Path'
        PesterTest                 = $true
    }
    Test-ModuleLocally @TestModuleLocallyInput -Verbose
    ```

3. Confirm to execute the script. After a moment, the terminal will show the test cases that are executed and should show one failed test

    <img src="./media/Lab7/failedTest.png" alt="Failed test" height="300">

    So why did it fail? Well, as per its description: The ReadMe outputs section should document all outputs defined in the template file. Before, you added a new template output, but the readme remained in its original state. 

# Step 3 - Re-generate the documentation

To update the readme, we provide another utility called `Set-ModuleReadMe`. This script again takes the template file path as an input an creates / updates almost all content of the module's readme file for you.

1. To get started you need to load the function first. Like before, load the script in path `utilities\tools\Set-ModuleReadMe.ps1`.

    <img src="./media/Lab7/loadScriptReadme.png" alt="load script for readme" height="50">

1. Next, you can invoke the function as follows

    ```PowerShell
    Set-ModuleReadMe -TemplateFilePath '<PathToTheUpdatedTemplate>' # Get the path via a right-click on the updated template file in VSCode & select 'Copy Path'
    ```

1. Once you confirmed the execution of the script you should notice that the module's `readme.md` file is marked as modified. 

    <img src="./media/Lab7/readMeUpdatedLog.png" alt="ReadMe Updated Log" height="100">

1. If you open version control to the left you should notice at least the template file `deploy.bicep` and readme file `readme.md` to be marked as changed. If you click on the readme specifically, a comparison view will open and show you that the readme was correctly updated

    <img src="./media/Lab7/readMeUpdated.png" alt="ReadMe Updated" height="250">

# Step 4 - Re-Run local test(s)

With the readme updated we can now re-run the test to confirm everything is in order.

1. Select again the terminal and either use your arrow-up key go through your previous commands until the get to the one that triggered the test (`Test-ModuleLocally`), or copy the same snipped from [Step 2](#step-2---run-local-tests).

    ```PowerShell
    $TestModuleLocallyInput = @{
        templateFilePath           = '<PathToTheUpdatedTemplate>' # Get the path via a right-click on the updated template file in VSCode & select 'Copy Path'
        PesterTest                 = $true
    }
    Test-ModuleLocally @TestModuleLocallyInput -Verbose
    ```

1. This time, however, none of the tests should fail:

    <img src="./media/Lab7/succeedTest.png" alt="Test succeeded" height="400">

# Step 5 - Upload your changes and run the module pipeline

Now that the contribution is implemented and the tests are green, you can continue to prepare everything for the subsequent pull request.

1. The first thing you have to do is to upload your changes. You can do this either via the terminal or by using the Git integration of VSCode. To keep it simple, this lab assumes you want to use the VSCode's git integration. If not already there, navigate to the source control menu to the left.

    <img src="./media/Lab7/sourceControl.png" alt="Open source control" height="140">

1. Here you first need to add the changed files to the commit. To do so, select the `+` icon next to `Changes` (appears when hovering)

    <img src="./media/Lab7/gitadd.png" alt="Git add" height="80">

1. Next, you should give the commit a meaningful message such as 'Added VM output to availability set' and can then click the checkmark symbol on the top to create the commit

    <img src="./media/Lab7/gitCommit.png" alt="Git commit" height="140">

1. Finally, you can push the changes to the repository by selecting the blue `Publish Branch` button

    <img src="./media/Lab7/gitpush.png" alt="Git push" height="80">

1. Back in your fork, navigate to `Actions`

    <img src="./media/Lab7/openActions.png" alt="Open actions" height="100">

1. From the list of actions to the left, select `Compute: AvailabilitySets` followed by the `Run workflow` dropdown to the right

    <img src="./media/Lab7/actionSelect.png" alt="Select actions" height="350">

1. Further select your branch from the `Branch:` dropdown

    <img src="./media/Lab7/actionBranch.png" alt="Select branch" height="200">

1. And finally trigger the pipeline with the default settings by selecting `Run workflow`
 
    <img src="./media/Lab7/actionTrigger.png" alt="Trigger run" height="200">

    Subsequently, the pipeline will start running through the the same tests you executed locally, but also execute the simulated deployment, followed by an actual test deployment in Azure.

1. While the pipeline is running, we can use the time to create a Pull Request. However, before doing so, you can take the chance to create a pipeline badge that you can attach to the later Pull Request. This badge will show the reviewer that the code changes were successfully validated & tested. To create a badge, first select the three dots (`...`) to the top right of the pipeline.

    <img src="./media/Lab7/badgeDropdown.png" alt="Badge dropdown" height="200">

1. In the opening pop up, you first need to select your branch and then click on the `Copy status badge Markdown`

    <img src="./media/Lab7/carmlStatusBadge.png" alt="Status badge" height="400">

# Step 6 - Create a Pull Request

In this step you will create the pull request. Do do so, perform the following tasks:

1. Navigate to `Pull requests` tab on the top. Here you should have multiple options: 
   - As you just uploaded changes to a branch, there is an automated popup that allows you to create a pull request for that branch directly
   - Alternatively, you can create a blank pull request via the `New pull request` button.
  
   Select now the `New pull request` button.

    <img src="./media/Lab7/pullRequestInit.png" alt="Pull request init" height="300">

1. This opens a new view in the `Azure/ResourceModules` (target) repository. Here, 
    - make you your `branch` is selected on the top right 
    - you provide a meaningful `title`
    - you provide a meaningful `description`. This description should include the pipeline badge you copied at the end of from [Step 5](#step-5---upload-your-changes-and-run-the-module-pipeline) 
    - select the correct `type of change`. In this case it would be a `New feature`

    <img src="./media/Lab7/openPRRaw.png" alt="Pull request raw" height="600">

1. Once you entered all the details, you can open a preview of the final look by selecting `Preview` on the top and finally select `Create pull request` to the bottom right.

    <img src="./media/Lab7/openPRPreview.png" alt="Pull request preview" height="500">

# Step 7 - Exclude environment-specific changes

Part of your pull request are 2 files that should not be pushed into the target repository as they contain details specific to your environment:
- `settings.json`
- `global.variables.json`

1. In your Visual Studio Code, open the `settings.json` file in the root directory

    <img src="./media/Lab7/settingsjson.png" alt="Settings JSON fork" height="180">

1. Now, open the `settings.json` of the `CARML` repository at _https://github.com/Azure/ResourceModules/blob/main/settings.json_, copy its content and overwrite it in your local file in VSCode 

    <img src="./media/Lab7/settingsCarml.png" alt="Settings JSON CARML" height="400">

1. Next, perform the same actions for your local `global.variables.json` in path `.github\variables\global.variables.json` and the corresponding file in the `CARML` at _https://github.com/Azure/ResourceModules/blob/main/.github/variables/global.variables.json_.


1. Once done, perform the same actions of [Step 5](#step-5---upload-your-changes-and-run-the-module-pipeline) to upload your changes to the branch. These changes will automatically be available in the Pull Request you created earlier.

---
---

If ready, proceed to the next lab: [Lab 8 - Build an ACR driven solution](./Lab%208%20-%20Build%20an%20ACR-driven%20solution)
