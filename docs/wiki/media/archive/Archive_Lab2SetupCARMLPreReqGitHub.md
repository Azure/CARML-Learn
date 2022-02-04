# GitHub Pre-Requisites

This section outlines how to set up the pre-requisites in GitHub. This includes
- Folk the repository
- Configure your GitHub fork including its connection to Azure
- Configure the relevant initial configuration files

---

### _Navigation_

- [Section pre-requisites](#section-pre-requisites)
- [Fork repository](#fork-repository)
- [Configure your fork](#configure-your-fork)
  - [Configure GitHub settings](#configure-github-settings)
  - [Configure code base](#configure-code-base) 

---

The GitHub fork we setup and configure in this section will act as your own custom CI solution to test & publish any module to host in your library. The enable its testing capabilities, this section is requires the information you collected during the [setup](./PreReqAzure) of your Azure environment.

# Section pre-requisites

This section requires to you
- Have an existing GitHub account
- Are logged into GitHub
- Installed 
  - Visual Studio Code
  - Git
  - PowerShell Core 
  - Windows Terminal

# Fork repository

The first thing you have to do is to fork the main repository. To do so, you have to perform the following steps:

1. Open to the repository via the URL [aka.ms/CARML](https://aka.ms/CARML)
   
    <img src="./media/PreReqGitHub/carmlMain.png" alt="Repository main" height="240">

1. Select the `Fork` button on the top right and select the organization you'd like to fork into. For this lab we recommend to use your own organization as this insures you will have all the permissions you need.

    <img src="./media/PreReqGitHub/carmlFork.png" alt="Repository Fork" height="180">

1. Once triggered, the repository will take a moment to be set up
   
    <img src="./media/PreReqGitHub/carmlForkingInProgress.png" alt="Forking in progess" height="160">

1. Once done, you will automatically be forwarded to your repository 

    <img src="./media/PreReqGitHub/carmlForkFinal.png" alt="Forked repository" height="250">

# Configure your fork

Now that you have your own copy of the library you have to set configure several GitHub settings as well as perform minor changes on the code base to get the solution up and running.

## Configure GitHub settings

The first thing you'll want to do is to configure several secrets that are leveraged by the solution's workflows. Most notably for example, the service connection.

To do that you have to perform the following steps in sequence:

1. Navigate to the repository's `settings`

    <img src="./media/PreReqGitHub/forkSettings.png" alt="Navigate to settings" height="100">

1. In the list of settings, navigate to `Secrets`. You can create a new repository secret by selecting `New repository secret` on the top right

    <img src="./media/PreReqGitHub/forkSettingsSecrets.png" alt="Navigate to secrets" height="500">

1. In the opening view, you can create a secret by providing a secret `Name`, a secret `Value`, followed by a click on the `Add secret` button

    <img src="./media/PreReqGitHub/forkSettingsSecretAdd.png" alt="Add secret" height="400">

    To get the solution up and running we need several pieces of information based on what we collected in the [Azure setup](./PreReqAzure) tutorial. So please create one secret for each entry in the following table and make sure you use the exact same naming:

    | Secret Name | Example | Description |
    | - | - | - |
    | `ARM_MGMTGROUP_ID` | `de33a0e7-64d9-4a94-8fe9-b018cedf1e05` | The group ID of the management group to test deploy modules of that level in. |
    | `ARM_SUBSCRIPTION_ID` | `d0312b25-9160-4550-914f-8738d9b5caf5` | The subscription ID of the   subscription to test deploy modules of that level in. |
    | `ARM_TENANT_ID` | `9734cec9-4384-445b-bbb6-767e7be6e5ec` | The tenant ID of the tenant to test   deploy modules of that level in. |
    | `DEPLOYMENT_SP_ID` | `de33a0e7-64d9-4a94-8fe9-b018cedf1e05` | This is the Principal (Object ID)   for the Service Principal used as the Azure service connection. It is used for Default Role Assignments when Modules are being deployed into Azure |
    | `AZURE_CREDENTIALS` | See below | See below |
    | <s>PLATFORM_REPO_UPDATE_PAT</s> | - | For this lab, this secret is optional and can be **ignored**. It would be used to sync the repository's `docs` folder with the repository's wiki. However, for the sake of this lab   we will ignore it for now. If you are interested, you can find further information [here](https://github.com/Azure/ResourceModules/blob/main/docs/wiki/PipelinesDesign.md#github-component-github-secrets). |
  
    - Special case: `AZURE_CREDENTIALS`, 
      This secret represents our service connection to Azure and its value is a   compressed JSON object that must match the following format:
      ```JSON
      {"clientId": "<Application ID>", "clientSecret": "<Application Secret>", "subscriptionId": "<ID of the subscription to test in>", "tenantId": "<ID of the tenant to test in>" }
      ```
      Make sure you create this object as one continuous string as shown above - using the information you collected during the Azure setup. If you're interested, you can find more information about this object [here](https://github.com/Azure/login#configure-deployment-credentials).
  

## Configure code base

As the platform tests services in Azure, you have to ensure that those services with globally unique naming requirements are set up accordingly. This must happen in two places
- The individual module parameter files
- The shared dependency pipeline

### Clone the repository

To perform these changes as quickly and easy as possible, we recommend to update the code base using a local clone of the code in Visual Studio Code (VSCode). To do so, please perform the following steps:

1. On the overview page of your fork, select the `<> Code` button to the right, and select the copy button in the opening pop up to copy the URL we need to clone the repository

    <img src="./media/PreReqGitHub/forkClone.png" alt="Clone fork" height="250">

1. On your local machine you can clone the repository to any location you desire. However, it is recommended to create for example the folder structure `C:/dev/CARML/lab` to keep the local file paths short

    <img src="./media/PreReqGitHub/localFolder.png" alt="Local folder" height="150">

1. In the folder you chose, perform a right-click and select `Open in Windows Terminal` to open a new PowerShell session with the path set to the current folder

    <img src="./media/PreReqGitHub/localTerminalOpen.png" alt="Open local terminal" height="230">

1. In the terminal type `git clone '<URL>'`, replace `<URL>` with the URL you copied before and confirm the execution

    <img src="./media/PreReqGitHub/localTerminalClone.png" alt="Clone in terminal" height="180">

1. Following the execution you will find that there is now a `ResourceModules` folder 

    <img src="./media/PreReqGitHub/localFolder.png" alt="Local Folder" height="150">

1. Next, back in the terminal, execute the command `code ./ResourceModules` to open Visual Studio Code in the clone repository folder folder

    <img src="./media/PreReqGitHub/localCodeCarml.png" alt="CARML in VSCode" height="500">


### Prepare the default prefix

To lower the barrier to entry and allow users to easily define their own naming conventions, we introduced a default "name prefix" that must be set during the solution setup. 

Essentially, each pipeline in CARML that deploys resources uses a logic that automatically replaces "tokens" (i.e. placeholders) with values that we store in a central location to facilitate maintenance. 
The "name prefix" mentioned above is stored in the `settings.json` file in the repository root directory.

So what you're required to do is to replace the `"value": "<replace>"` of token `namePrefix` to a different value.

```json
{
    "parameterFileTokens": {
        (...)
        "localTokens": {
            "tokens": [
                {
                    "name": "namePrefix",
                    "value": "<replace>"
                }
            ]
        }
    }
}
```

> **NOTE:** As the prefix is also used for all those resources that require a globally unique name, you should choose a value that is likewise unlikely to be already used somewhere. At the same time, the value should not be too long, as some resources have length restrictions. 

Our recommendation is to use a triple like the first letter of your first name, and the first two letters of your second name. For example `Max Musterman` would result into `mmu`.



## Enable actions

Finally, the 'GitHub Actions' are disabled by default. Hence, in order to continue with the rest of the lab and execute any pipelines you have to enable them first.

To do so, perform the following steps:

1. Navigate to the `Actions` tab on the top of the repository page

    <img src="./media/PreReqGitHub/actionsOverview.png" alt="Navigate to actions" height="150">

1. Next, select '`I understand my workflows, go ahead an enable them`' 

    <img src="./media/PreReqGitHub/actionsEnable.png" alt="Enable Actions" height="380">

## Create a branch

By default, CARML employs pipeline triggers to automate for example the publishing of a new module once a corresponding PR is merged. 

To this end, the trigger is set up to trigger upon any changes to the `main` branch if any module or pipeline file was modified.

As you don't want to accidentally trigger any pipelines, you should hence create a branch to perform your tasks on throughout the rest of the lab.

To do so, navigate back to your local Visual Studio code, navigate to the `Terminal` to the bottom, and execute the following PowerShell commands:

```PowerShell
git checkout -b 'carmlLab'
git push --set-upstream 'origin' 'carmlLab'
```