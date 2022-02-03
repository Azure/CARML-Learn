In this lab, you'll set-up **CAML** on your own environment. This set-up can be used to mimic the module factory we use at your customer/company, or to perform and end to end testing for contribution purposes.

### _Navigation_
- [Step 1 - Create Azure Service Principal](#step-1---create-azure-service-principal)
- [Step 2 - Service principle access to subscription](#step-2---service-principle-access-to-subscription)
- [Step 3 - Create your fork](#step-3---create-your-fork)
- [Step 4 - Configure your repository](#step-4---configure-your-repository)
- [Step 5 - Configure code base](#step-5---configure-code-base)
- [Step 6 - Create a branch](#step-6---create-a-branch)
- [Step 7 - Enable actions](#step-6---enable-actions)

---

# Step 1 - Create Azure Service Principal

CARML tests the deployments and stores the module artifacts in an Azure subscription. To do so, it requires a service principal with access to it.

This step will guide you in the creation of the Service Principle and the gathering of the required values that will be used in the next steps.

For this lab, it is enough to just write them temporarily in for example Notepad. You should have notes the following pieces:

- Application ID
- Service Principal Object ID
- Service Principal Secret (password)
- Tenant ID
- Subscription ID
- Parent Management Group

1. Open to the Azure Portal via the URL [https://portal.azure.com](https://portal.azure.com)
   
    <img src="./media/PreReqAzure/portalHome.png" alt="Portal Home" height="200">

1. Navigate to Azure Active Directory (Azure AD) by using for example the search bar on the top 

    <img src="./media/PreReqAzure/portalSearchAAD.png" alt="Portal Search AAD" height="190">

1. Here we want to do 2 things:
   - Make note of your `Tenant ID` in the displayed `Overview` for later reference
   - Further select `App registrations` in the blade to the left

    <img src="./media/PreReqAzure/portalAzureAD.png" alt="Portal AAD Home" height="400">

1. In the opening view select `+ New registration` on the top

    <img src="./media/PreReqAzure/portalAzureADAppRegistrations.png" alt="Portal AAD App Registration Overview" height="180">

1. In the opening form, please provide a name of your choice and select `Register`. All other options can remain as is.

    <img src="./media/PreReqAzure/portalAzureADAppCreate.png" alt="Portal AAD App Registration" height="600">

1. This will open the created application's overview. Here we again want to do 2 things:
   - Make note of the `Application (client) ID` for later reference
   - Navigate to the application's underlying service principal by selecting it's name on the right side of the application's overview

    <img src="./media/PreReqAzure/portalAzureADAppView.png" alt="Portal AAD App View" height="200">

1. Here you have to perform 2 tasks:
   - Make note of the service principal's `Object ID`    for later reference
   - Further, navigate back to the created application's overview by going back in your browser

    <img src="./media/PreReqAzure/portalAzureADAppViewSPView.png" alt="" height="200">

1. Back on the application, you again have to perform 2 tasks:
   - Select `Certificates & secrets` in the blade to the left
   - Select `+ New client secret` in the opening view to the right

    <img src="./media/PreReqAzure/portalAzureADAppSecretCreate.png" alt="Portal AAD App Secret Create" height="300">

1. Now enter a name for the secret, and click on `Add`

    <img src="./media/PreReqAzure/portalAzureADAppSecretCreateBlade.png" alt="Portal AAD App Secret Create Blade" height="150">

1. The previous step created a new secret for the application which is shown to us now. Please make note of the secret value as it will only be visible until we leave this view.

    <img src="./media/PreReqAzure/portalAzureADAppSecretView.png" alt="Portal AAD App Secret View" height="300">

# Step 2 - Service principle access to subscription

Now that we have a new service principal, we must grant it access on the subscription we want to test later deployments in.

1. As a first step, you have to navigate to you subscription by using for example the search bar on the top

    <img src="./media/PreReqAzure/portalSubscriptionSearch.png" alt="Subscription search" height="170">

1. Following, select the subscription you want to grant access to. In the below example it is called `Visual Studio Enterprise Subscription`

    <img src="./media/PreReqAzure/portalSubscriptionSelect.png" alt="Subscription select" height="230">

1. This brings you to the overview of your subscription. Here you need to perform 3 tasks:
   - Make note of the `Subscription ID` for later reference
   - Make note of the `Parent Management Group` for later reference
   - Further select  `Access control (IAM)` in the blade to the left

    <img src="./media/PreReqAzure/portalSubscriptionView.png" alt="Subscription overview" height="220">

1. In the opened `Access control (IAM)` view, select `+ Add` on the top 

    <img src="./media/PreReqAzure/portalSubscriptionIAM.png" alt="Role assignment overview" height="170">

1. In the opening drop-down, select `Add role assignment`

    <img src="./media/PreReqAzure/portalSubscriptionIAMAdd.png" alt="Role assignment init" height="170">

1. This opens a new view with 3 tabs we have to navigate through. First, you must select the `Role` we want to assign. For this lab we recommend to use the `Owner` role, as the CI platform tests both resource deployments, as well as role assignments. If you don't want to assign `Owner`, you can also choose `Contributor` in combination with `User Access Administrator`. In either case, you can you the search to search for the roles you want to assign and select them in the list below it.

    <img src="./media/PreReqAzure/portalSubscriptionIAMAddRole.png" alt="Role assignment select role" height="260">

1. Next, navigate to the `Members` tab and click on `+ Select members`

    <img src="./media/PreReqAzure/portalSubscriptionIAMAddRoleMember.png" alt="Role assignment member initial" height="275">

1. In the opening blade, search for the name of the application you created (in this example it is `MyApplication`), make sure you **select** the application, and click on `Select` on the bottom of the blade 

    <img src="./media/PreReqAzure/portalSubscriptionIAMAddRoleMemberBlade.png" alt="Role assignment select member" height="250">

1. This will close the blade and show the application in the `Members` view as shown in the image below

    <img src="./media/PreReqAzure/portalSubscriptionIAMAddRoleMemberView.png" alt="Role assignment member selected" height="275">

1. Finally, select the `Review + assign` tab which shows you an overview of the configuration and click on the `Review + assign` button on the bottom to conclude the role assignment

    <img src="./media/PreReqAzure/portalSubscriptionIAMAddFinal.png" alt="Role assignment Review" height="310">

1. This will close the view and we can confirm that the role assignment worked by searching for the application in the searching and selecting your application in the `Check access` section. Be aware that it may take a few seconds for the role assignment to finish

    <img src="./media/PreReqAzure/portalSubscriptionIAMViewSearch.png" alt="Role assignment check access search" height="300">

1. In the opening blade you can see that the application indeed is configured with the expected role(s) for the current subscription.

    <img src="./media/PreReqAzure/portalSubscriptionIAMViewSearchConfirm.png" alt="Role assignment check access" height="120">

# Step 3 - Create your fork

In **CARML**, you can't work directly in the `main` branch, so the first action to be taken is to to _fork_ the repository.

> One of the fundamental features in version control systems, branching and forking are fundamentals to collaboration.
>
> A **branch** in Git creates another line of development in the project without affecting the main branch.
>
> **Fork**, on the other hand, is a clone of the repository on a different organization and you can do it even if you don't have contribution rights on the main repo.

1. Open to the repository via the URL [aka.ms/CARML](https://aka.ms/CARML)

    <img src="./media/PreReqGitHub/carmlMain.png" alt="Repository main" height="240">

1. Select the `Fork` button on the top right and select the organization you'd like to fork into. For this lab we recommend to use your own organization as this insures you will have all the permissions you need.

    <img src="./media/PreReqGitHub/carmlFork.png" alt="Repository Fork" height="180">

1. Once triggered, the repository will take a moment to be set up

    <img src="./media/PreReqGitHub/carmlForkingInProgress.png" alt="Forking in progess" height="160">

1. Once done, you will automatically be forwarded to your repository

    <img src="./media/PreReqGitHub/carmlForkFinal.png" alt="Forked repository" height="250">

# Step 4 - Configure your repository

Now you need to configure several secrets that are leveraged by the solution's workflows. Most notably for example, the service connection.

To do that you have to perform the following steps in sequence:

1. Navigate to the repository's `settings`

    <img src="./media/PreReqGitHub/forkSettings.png" alt="Navigate to settings" height="100">

1. In the list of settings, expand `Secrets` and select `Actions`. You can create a new repository secret by selecting `New repository secret` on the top right

    <img src="./media/PreReqGitHub/forkSettingsSecrets.png" alt="Navigate to secrets" height="600">

2. In the opening view, you can create a secret by providing a secret `Name`, a secret `Value`, followed by a click on the `Add secret` button

    <img src="./media/PreReqGitHub/forkSettingsSecretAdd.png" alt="Add secret" height="600">

    The values of the secrets are the ones we collected in the [Azure setup](./PreReqAzure) tutorial. You'll need to create one secret for each entry in the following table and make sure you use the exact same naming:

    | Secret Name | Example | Description |
    | - | - | - |
    | `ARM_MGMTGROUP_ID` | `de33a0e7-64d9-4a94-8fe9-b018cedf1e05` | The group ID of the management group to test deploy modules of that level in. |
    | `ARM_SUBSCRIPTION_ID` | `d0312b25-9160-4550-914f-8738d9b5caf5` | The subscription ID of the subscription to test deploy modules of that level in. |
    | `ARM_TENANT_ID` | `9734cec9-4384-445b-bbb6-767e7be6e5ec` | The tenant ID of the tenant to test   deploy modules of that level in. |
    | `DEPLOYMENT_SP_ID` | `de33a0e7-64d9-4a94-8fe9-b018cedf1e05` | This is the Principal (Object ID)   for the Service Principal used as the Azure service connection. It is used for Default Role Assignments when Modules are being deployed into Azure |
    | `AZURE_CREDENTIALS` | See below | See below |
    <!-- | <s>PLATFORM_REPO_UPDATE_PAT</s> | - | For this lab, this secret is optional and can be **ignored**. It would be used to sync the repository's `docs` folder with the repository's wiki. However, for the sake of this lab we will ignore it for now. If you are interested, you can find further information [here](https://github.com/Azure/ResourceModules/blob/main/docs/wiki/PipelinesDesign.md#github-component-github-secrets). | -->
  
    - Special case: `AZURE_CREDENTIALS`,
      This secret represents our service connection to Azure and its value is a compressed JSON object that must match the following format:

      ```JSON
      {"clientId": "<Application ID>", "clientSecret": "<Application Secret>", "subscriptionId": "<ID of the subscription to test in>", "tenantId": "<ID of the tenant to test in>" }
      ```

      Make sure you create this object as one continuous string as shown above - using the information you collected during the Azure setup. If you're interested, you can find more information about this object [here](https://github.com/Azure/login#configure-deployment-credentials).


# Step 5 - Configure code base

As the platform tests services in Azure, you have to ensure that those services with globally unique naming requirements are set up accordingly. This must happen in two places
- The individual module parameter files
- The shared dependency pipeline

## Clone the repository

To perform these changes as quickly and easy as possible, we recommend to update the code base using a local clone of the code in Visual Studio Code (VSCode). To do so, please follow the following sequence of steps:

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

    <img src="./media/PreReqGitHub/localCodeCarml.png" alt="CARML in VSCode" height="450">


## Prepare the default prefix

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

## Step 6 - Create a branch

By default, CARML uses pipeline triggers to automate for example the publishing of a new module once a corresponding PR is merged. 

To this end, the trigger is set up to look for any changes in the `main` branch if any module or pipeline file was modified.

As you don't want to accidently trigger any pipelines, you should hence create a branch to perform your tasks on throughout the rest of the lab.

To do so, navigate back to your local Visual Studio code, navigate to the `Terminal` to the bottom, and execute the following PowerShell commands:

```PowerShell
git checkout -b 'carmlLab'
git push --set-upstream 'origin' 'carmlLab'
```

# Step 7 - Enable actions

Finally, the 'GitHub Actions' are disabled by default. Hence, in order to continue with the rest of the lab and execute any pipelines you have to enable them first.

To do so, follow the following sequence of steps:

1. Navigate to the `Actions` tab on the top of the repository page

    <img src="./media/PreReqGitHub/actionsOverview.png" alt="Navigate to actions" height="150">

1. Next, select '`I understand my workflows, go ahead an enable them`' 

    <img src="./media/PreReqGitHub/actionsEnable.png" alt="Enable Actions" height="380">
