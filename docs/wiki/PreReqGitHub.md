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

This section requires to you have
- An existing GitHub account
- Are logged into GitHub

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
  | `ARM_SUBSCRIPTION_ID` | `d0312b25-9160-4550-914f-8738d9b5caf5` | The subscription ID of the subscription to test deploy modules of that level in. |
  | `ARM_TENANT_ID` | `9734cec9-4384-445b-bbb6-767e7be6e5ec` | The tenant ID of the tenant to test deploy modules of that level in. |
  | `DEPLOYMENT_SP_ID` | `de33a0e7-64d9-4a94-8fe9-b018cedf1e05` | This is the Principal (Object ID) for the Service Principal used as the Azure service connection. It is used for Default Role Assignments when Modules are being deployed into Azure |

  There is one additional secret we **must** create but needs a specific format. This secret is called `AZURE_CREDENTIALS`, represents our service connection to Azure and its value is a compressed JSON object that must match the following format:
  ```JSON
  {"clientId": "<Application ID>", "clientSecret": "<Application Secret>", "subscriptionId": "<ID of the subscription to test in>", "tenantId": "<ID of the tenant to test in>" }
  ```
  Make sure you create this object as one continuous string as shown above - using the information you collected during the Azure setup. If you're interested, you can find more information about this object [here](https://github.com/Azure/login#configure-deployment-credentials).

  > Note: In theory there is one additional secret called `PLATFORM_REPO_UPDATE_PAT` which is used to sync the repository's `docs` folder with the repository's wiki. However, for the sake of this lab we will ignore it for now. If you are interested, you can find further information [here](https://github.com/Azure/ResourceModules/blob/main/docs/wiki/PipelinesDesign.md#github-component-github-secrets). 

## Configure code base