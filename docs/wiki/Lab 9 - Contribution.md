In this lab, you will learn how a contribution to the source repository (in this example the CARML main repository) would look like. At a customer, the same approach could be used to allow teams to contribute to a central library.

### _Navigation_
- [Step 1 - Implement the contribution](#step-1---implement-the-contribution)
- [Step 2 - Re-generate the documentation](#step-2---re-generate-the-documentation)
- [Step 3 - Run local test(s)](#step-3---run-local-tests)
- [Step 4 - Upload your changes and run the module pipeline](#step-4---upload-your-changes-and-run-the-module-pipeline)
- [Step 5 - Create a PR](#step-5---create-a-pr)
- [Step 6 - Cleanup unintended changes](#strep-6---cleanup-unintended-changes)
  
---

# Step 1 - Implement the contribution
<!-- E.g. update parameter metadata -->

The first step of any contribution is its implementation. For the sake if this lab, we will suggest a simple contribution that will allow you the make use of some of our tools:

1. In your local VSCode, navigate via `arm`, on to `Microsoft.Compute` and finally to the `availabilitySets`'s template `deploy.bicep`

    <img src="./media/Lab9/asDeployBicepSelect.png" alt="Availability Set VSCode" height="300">

1. A simple contribution to perform is the addition of an additional `output` the bicep template will return upon successful execution. As we have seen, these outputs are particularly useful when multiple modules are orchestrated together. Please add the following snippet to the end of the file: 
   
    ```bicep
    @description('A list of references to all virtual machines in the availability set.')
    output subnets array = availabilitySet.properties.virtualMachines
    ```

# Step 2 - Run local test(s)

CARML comes with a number of tools that you can use to perform several automated tasks for you. One of these tools can be use to perform the same tests locally as you would see in the pipeline. To do so, please perform the following tasks:

1. Navigate navigate through the folders `utilities` and `tools` and click on the script `Test-ModuleLocally.ps1`.

    <img src="./media/Lab9/TestLocalOpen.png" alt="Open Test Local script" height="250">

1. Next, you must load the function implemented in the script by triggering the `Run` button to the top right of VSCode.

    <img src="./media/Lab9/loadScript.png" alt="Local script" height="50">

    > ***Note***: Should VSCode ask you (in the terminal) whether you are sure you want to execute the script, please confirm
    > <img src="./media/Lab9/allowsScript.png" alt="Enable execution" height="100">


# Step 3 - Re-generate the documentation

# Step 4 - Run local test(s)

# Step 5 - Upload your changes and run the module pipeline
<!--
1. In your fork, navigate to the list of pipelines in the lower end of the repository overview and select the name of the `Key Vault` module in the left column

    <img src="./media/Lab9/repoOverview.png" alt="Repo Overview" height="600">
-->


# Step 6 - Create a PR
<!-- Attach the badge -->

# Strep 7 - Cleanup unintended changes
<!-- Undo changes to e.g. global variables & settings.json -->
