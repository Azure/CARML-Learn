In this lab, you will learn how publish modules, both for releases and prereleases. You will also publish your first modules to the Bicep Registry.

### _Navigation_
- [Step 1 - Publish prerelease for a module](#step-1---publish-prerelease-for-a-module)
- [Step 2 - Prepare to publish a release](#step-2---prepare-to-publish-a-release)

---

# Step 1 - Publish prerelease for a module

- Create and check out a new branch called `dev/routeTable`.
- Change: `arm\Microsoft.Network\routeTables\version.json` - Update version to be `0.1`.
- Save, commit and push your branch.
- Run the workflow in GitHub.
  - Find the workflow for `Network: RouteTables`.
  - Click on the "Run workflow" button, select branch `dev/routeTable` and check the box for `Publish prerelease`.
  - Wait for the workflow to run, verify that the workflow has published a prerelease version (`x.x.x-prerelease`) to the Bicep registry.
  - Verify by going to the Azure portal and investigating the bicep registry. Find the `bicep/modules/microsoft.network.routetables` repository. Check it for published versions.

# Step 2 - Prepare to publish a release

- While still on branch `dev/routeTable`, Update the `version.json` files from all the modules used in Lab 1. Change the version number to `0.1`.
- Save, commit and push your changes.
- Merge your branch with `main` by creating a pull request on GitHub and merging it.
- Go to one of the modules workflows and pay attention to the publish step.
  - When a change happends to any of the module files (as configured in the `on:` or `trigger:` sections of the workflow/pipeline file), the workflow runs.
  - The publishing stage of the workflow will:
    - Look for changed files in the module and child modules folders.
    - Find the nearest template file (deploy.* in same folder as the changed file or parent folder).
    - Find the corresponging `version.json` file and get the `major` and `minor` version numbers. The `patch` number is calculated from 'git hight' (number of commits from base commit). If the workflow/pipeline is run on a non `main`/`master` branch, '-prerelease' is added to the version number.
- When you have seen that the workflow has completed for the module you chose, go to the Actions overview and filter the view to show only successful workflow runs.
- Check that the others complete, and verify their published versions in the Azure portal.
