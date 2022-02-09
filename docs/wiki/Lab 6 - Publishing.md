In this lab, you will learn how to publish modules, both for releases and prereleases. You will also publish your first modules to the Bicep Registry.

### _Navigation_
- [Step 1 - Publish prerelease for a module](#step-1---publish-prerelease-for-a-module)
- [Step 2 - Run the workflow in GitHub](#step-2---run-the-workflow-in-github)
- [Step 3 - Verify the published module](#step-3---verify-the-published-module)
- [Step 4 - Prepare to publish a release](#step-4---prepare-to-publish-a-release)
---

# Step 1 - Publish prerelease for a module

1. In your local VSCode, find the file `arm\Microsoft.Network\routeTables\version.json` and update the version property to be `0.1`.

    <img src="./media/Lab6/1.png" alt="update the version property in the version.json file to be 0.1" height=180>

1. Next, upload your changes in the same manner as you did before, add your changes to the commit, create a commit with a corresponding commit message (e.g. `Updated route table version to 0.1`) and push the changes to GitHub

    <img src="./media/Lab9/gitpush.png" alt="Git push" height=100>

# Step 2 - Run the workflow in GitHub

1.  Find the workflow for `Network: RouteTables` in the `Actions` tab in GitHub.

1.  Click on the "Run workflow" button, select branch `dev/routeTable` and check the box for `Publish prerelease`.

    <img src="./media/Lab6/triggerPreRelease.png" alt="Manually run the workflow for Network: RouteTables with prerelease flag" height=250>

1. Wait for the workflow to run, verify that the workflow has published a prerelease version (`0.1.x-prerelease`) to the Bicep registry.

    <img src="./media/Lab6/acrPublish.png" alt="Verify the workflow logs seeing that it published to the registry">

# Step 3 - Verify the published module

One of the ways you can verify whether the publishing worked is by going to the Azure portal and investigating the bicep registry. 

1. To get there, you first have to navigate to the resource group containing the container registry (by default `artifacts-rg`). In it, search for the ACR that you specified in [Lab 2](./Lab%202%20-%20Setup%20CARML%20prerequisites#Set-the-container-registry-unique-name).

    <img src="./media/Lab6/acrSelect.png" alt="Find the ACR" height=500>   

1. Next, find the `bicep/modules/microsoft.network.routetables` repository. 
    
    <img src="./media/Lab6/acrRepo.png" alt="Verify in the Azure portal what version was published" height=350>

1. Check the repository for the published version.

    <img src="./media/Lab6/acrVersion.png" alt="The published version" height=250>

# Step 4 - Prepare to publish a release

Next, you will create an actual release, that is, publish modules without the prerelease flag. To do so, proceed with the following steps: 

1. First, you need to create a pull request. As `carmlLab` was the last branch you worked on, GitHub should create a banner with a button `Compare & Pull request` for you, once you navigate to the `<> Code` tab (refresh the page if it does not show up automatically).

   <img src="./media/Lab6/prbanner.png" alt="Create a PR" height=200>

1. In the PR, provide a meaningful description and if you're interested also check the file changes (should be around 6 files) to make sure everything seems in order. Once you're ready, go ahead an merge the pull request.

   <img src="./media/Lab6/prMerge.png" alt="Merge a PR" height=200>


1. Check the workflow run that was triggered by the merge (push) to main and see what happens with the published version.

    <img src="./media/Lab6/acrPublish.png" alt="Check the workflow run of the routeTables once the PR is merged">


- Back in VSCode, change to main and sync main branch with the newest changes.

```pwsh
  git checkout main
  git pull
```

- Create and check out a new branch called `dev/prereqModules`.

  ```pwsh
    git checkout -b dev/prereqModules origin/main
  ```

- Now we do the same exercise for all the modules used in Lab 1 and those needed for next lab. Change the version number to `0.1` on the following modules:
  - `Microsoft.Resources/resourceGroups`
  - `Microsoft.Storage/storageAccounts`
  - `Microsoft.KeyVault/vaults`
  - `Microsoft.OperationalInsights/workspaces`
  - `Microsoft.Insights/components`
  - `Microsoft.MachineLearningServices/workspaces`
- Save (`Ctrl+s`), commit and push your changes.

  ```pwsh
    git commit -a -m "update version to 0.1"
    git push origin dev/routeTable
  ```

- Merge your branch with `main` by creating a pull request on GitHub and merging it.
- Go to one of the modules workflows and pay attention to the publish step.

   <img src="./media/Lab6/7.png" alt="Updates for storageAccounts">

  - When a change happends to any of the module files (as configured in the `on:` or `trigger:` sections of the workflow/pipeline file), the workflow runs.
  - The publishing stage of the workflow will:
    - Look for changed files in the module and child modules folders.
    - For a detected change:
      - Find the nearest template file (deploy.* in same folder as the changed file or parent folder).
      - Find the corresponging `version.json` file and get the `major` and `minor` version numbers. The `patch` number is calculated from 'git hight' (number of commits from base commit). If the workflow/pipeline is run on a non `main`/`master` branch, '-prerelease' is added to the version number.
      - Finds all parent module files, and get their version data using the same logic as mentioned over.
    - Publish each changed child module and parents, using the calculated version numbers, publish a `major` and `major.minor` version for easy selection of 'latest' version.
- When you have seen that the workflow has completed for the module you chose, go to the Actions overview and filter the view to show only successful workflow runs.
- Check that the others complete, and verify their published versions in the Azure portal.

Proceed to the next lab: [Lab 7 - Build an ACR driven solution](./Lab%207%20-%20Build%20an%20ACR-driven%20solution.md)
