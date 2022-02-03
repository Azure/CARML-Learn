# Common Azure Resource Modules Lab

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
  - [Lab 6: Publishing](./Lab%205%20-%20Extend%20test%20coverage)
  - [Lab 7: Build an ACR-driven solution](./Lab%207%20-%20Build%20an%20ACR-driven%20solution)
  - [Lab 9: Contribution](./Lab%209%20-%20Contribution)

# Pre-Requisites

This lab requires a number of fundamental pre-requisites you need in order to complete the lab in its entirety. These are
- An active Azure subscription
- `Owner` (or `Contributor` + `User Access Administrator`) permissions on set subscription
- Access to the subscriptions tenant with permissions to create applications
- Installed 
  - [Visual Studio Code](URL 'https://code.visualstudio.com/Download') + [Bicep extension](URL 'https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/install')
  - [Azure CLI](URL 'https://docs.microsoft.com/en-us/cli/azure/install-azure-cli')
    - [Bicep CLI](URL 'https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/install#azure-cli')
  - Azure CLI Extensions
    - Bicep
  - [Git](URL 'https://git-scm.com/downloads')
  - [PowerShell Core](URL 'https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell?view=powershell-7.2')
  - [Windows Terminal](URL 'https://www.microsoft.com/en-US/p/windows-terminal/9n0dx20hk701?activetab=pivot:overviewtab')
  - [PowerShell modules] (URL 'https://docs.microsoft.com/en-us/powershell/azure/install-az-ps?view=azps-7.1.0')
    - Az.Accounts
    - Az.Resources  