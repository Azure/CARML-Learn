# Common Azure Resource Modules Library - Lab

This Wiki hosts the lab for the _'Common Azure Resource Modules Library (CARML)'_

### _Navigation_
- Archive
  - [Original SPARK submission](./OriginalSubmission)
  - [Original Mock](./OriginalMock)
- Labs
  - [Lab 1: Use CARML to deploy infrastructure](./Lab%201%20-%20Use%20CARML%20to%20deploy%20infrastructure)
  - [Lab 2: Setup CARML prerequisites](./Lab%202%20-%20Setup%20CARML%20prerequisites)
  - [Lab 3: Deploy dependencies](./Lab%203%20-%20Deploy%20dependencies)
  - [Lab 4: First pipeline run](./Lab%204%20-%20First%20pipeline%20run)
  - [Lab 5: Extend test coverage](./Lab%205%20-%20Extend%20test%20coverage)
  - [Lab 6: Publishing](./Lab%206%20-%20Publishing)
  - [Lab 7: Contribution](./Lab%207%20-%20Contribution)
  - [Lab 8: Build an ACR-driven solution](./Lab%208%20-%20Build%20an%20ACR-driven%20solution)
  - [Lab 9: Interoperability](./Lab%209%20-%20Interoperability)

# Pre-Requisites

This lab requires a number of fundamental pre-requisites you need in order to complete the lab in its entirety. These are
- An active Azure subscription
- `Owner` (or `Contributor` + `User Access Administrator`) permissions on set subscription
- Access to the subscriptions tenant with permissions to create applications
- Installed 
  - [Visual Studio Code](https://code.visualstudio.com/Download)
    - \+ Extension: [Bicep](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/install)
  - [Bicep CLI](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/install#manual-with-powershell)
  - [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)
    - \+ Extension: [Bicep](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/install#azure-cli)
  - [Git](https://git-scm.com/downloads)
    - [Git Guide](https://rogerdudler.github.io/git-guide/)
    > ***Note:*** If just installed, don't forget to set both your git username & password
    > ```PowerShell
    > git config --global user.name "John Doe"
    > git config --global user.email johndoe@example.com
    > ```
  - [Windows Terminal](https://www.microsoft.com/en-US/p/windows-terminal/9n0dx20hk701?activetab=pivot:overviewtab)
  - [PowerShell Core](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell?view=powershell-7.2)
    - \+ Module: `Az.Accounts`
    - \+ Module: `Az.Resources`  
    - \+ Module: `Pester` with minimum version `5.3.1`
