# Azure Pre-Requisites

This section outlines how to set up the pre-requisites in Azure. This includes
- Create a service principal
- Create a secret for set service principal
- Assign `Owner` permissions on a subscription
  
---

### _Navigation_

- [Service Principal](#actions-in-azure-ad)
- [Assign permissions](#actions-on-a-subscription)

---


The service principal we create will be used as a service connection to let GitHub interact with Azure. This requires us to create a principal first, and then assign it permissions on the subscription we want to interact with in GitHub.

Note, that throughout the documentation below you will be asked to note down several pieces of information. For this lab, it is enough to just write them temporarily in for example Notepad. At the end of this tutorial you should have notes the following pieces:
- Application: Application ID
- Application: Service Principal Object ID
- Application: Secret
- Tenant: Tenant ID
- Subscription: Subscription ID

# Actions in Azure AD

1. Open to the Azure Portal via [https://portal.azure.com](https://portal.azure.com)
   
    <img src="./media/PreReqAzure/portalHome.png" alt="Portal Home" height="240">

1. Navigate to Azure Active Directory (Azure AD) by using for example the search bar on the top 

    <img src="./media/PreReqAzure/portalSearchAAD.png" alt="Portal Search AAD" height="240">

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

    <img src="./media/PreReqAzure/portalAzureADAppSecretCreate.png" alt="Portal AAD App Secret Create" height="350">

1. Now enter a name for the secret, and click on `Add`

    <img src="./media/PreReqAzure/portalAzureADAppSecretCreateBlade.png" alt="Portal AAD App Secret Create Blade" height="150">

1. The previous step created a new secret for the application which is shown to us now. Please make note of the secret value as it will only be visible until we leave this view.

    <img src="./media/PreReqAzure/portalAzureADAppSecretView.png" alt="Portal AAD App Secret View" height="300">

# Actions on a subscription

Now that we have a new service principal, we must grant it access on the subscription we want to test later deployments in. 

1. As a first step, you have to navigate to you subscription by using for example the search bar on the top
   
    <img src="./media/PreReqAzure/portalSubscriptionSearch.png" alt="Subscription search" height="170">

1. Following, select the subscription you want to grant access to. In the below example it is called `Visual Studio Enterprise Subscription`
   
    <img src="./media/PreReqAzure/portalSubscriptionSelect.png" alt="Subscription select" height="230">

1. This brings you to the overview of your subscription. Here you need to perform 2 tasks:
   - Make note of the `Subscription ID` for later reference
   - Further select  `Access control (IAM)` in the blade to the left
   
    <img src="./media/PreReqAzure/portalSubscriptionView.png" alt="Subscription overview" height="120">

1. In the opened `Access control (IAM)` view, select `+ Add` on the top 
   
    <img src="./media/PreReqAzure/portalSubscriptionIAM.png" alt="Role assignment overview" height="170">

1. In the opening drop-down, select `Add role assignment`
   
    <img src="./media/PreReqAzure/portalSubscriptionIAMAdd.png" alt="Role assignment init" height="170">

1. This opens a new view with 3 tabs we have to navigate through. First, you must select the role we want to assign. For this lab we recommend to use the `Owner` role, as the CI platform tests both resource deployments, as well as role assignments. If you don't want to assign `Owner`, you can also choose `Contributor` in combination with `User Access Administrator`. In either case, you can you the search to search for the roles you want to assign and select them in the list below it.
   
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
