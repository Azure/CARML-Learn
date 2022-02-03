In this LAB you will explore how a module runs through the first two pipeline stages, the local validation, as well as validation through Azure.

### _Navigation_
- [Step 1 - Modify a parameter file](#Step-1---Modify-a-parameter-file)
- [Step 2 - Test the module](#Step-2---Test-the-module)
- [Step 3 - Build solution](#Step-3---Build-solution)
- [Step 4 - Merge the pull request](#Step-4---Merge-the-pull-request)
- [Step 5 - Verify publishing results](#Step-5---Verify-publishing-results)
---

# Step 1 - Test the module

After this preparation phase, we're sure you would like to see some Action(s)!

1. Click on `Actions`

    <img src="./media/FirstPipelineRun/actions-menu.png" alt="Actions menu" height="100">

2. Look for the `Network: RouteTables` workflow

    <img src="./media/FirstPipelineRun/actionSelection.png" alt="Actions selection" height="100">

3. Select the `Run workflow` button. Make sure the selected branch is the one you created in Lab 2 and click on `Run workflow` on the bottom.

    <img src="./media/FirstPipelineRun/public-ip-workflow.png" alt="Run workflow on branch" height="500">

# Step 3 - Verify the workflow run correctly

Let's now see what happened.

1. First of all, if you click on the run, you should see jobs have run successfully.

    <img src="./media/FirstPipelineRun/workflow-result-git.png" alt="PIP run ok" height="500">

    > You will also notice the `Publish module` step has been skipped. This is because the workflows will only execute this job if run from the `main` branch, so that you do your test before the publishing happens.

1. You can also check the result on the Azure portal. You will notice that a resource group named `validation-rg` has been created. You can verify that new deployments happened in the Resource Group

    <img src="./media/FirstPipelineRun/workflow-result-azure.png" alt="PIP run ok, RG" height="400">

    > You can notice the resource group has no resources now. By default, the last step of the deployment will also remove what has been deployed, to keep the testing sanbox subscription cost as low as possible.
    >
    > You can modify this behaviour by unflagging `Remove deployed module` flag when running the workflow

---

[Now proceed to the next LAB](./Lab%205%20-%20Extend%20test%20coverage)
