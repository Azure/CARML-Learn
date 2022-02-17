
# Pre-Requisites

This lab requires a number of fundamental pre-requisites you need in order to complete the lab in its entirety. These are
- A GitHub Account
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
    > git config --global user.email "johndoe@example.com"
    > ```
  - [Windows Terminal](https://www.microsoft.com/en-US/p/windows-terminal/9n0dx20hk701?activetab=pivot:overviewtab)
  - [PowerShell Core](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell?view=powershell-7.2)
    - \+ Module: `Az.Accounts`
    - \+ Module: `Az.Resources`
    - \+ Module: `Pester` with minimum version `5.3.1`

## Scripts

<details>
<summary><b>Check version script</b></summary>

```powershell
    az --version
    bicep --version
    git --version
    code --version
    pwsh --version
    Get-Module -Name 'Az.Accounts' -ListAvailable
    Get-Module -Name 'Az.Resources' -ListAvailable
    Get-Module -Name 'Pester' -ListAvailable
```
</details>


<details>
<summary><b>Install pre-requisites script (only Win10/11)</b></summary>

> **Note:** You must have `winget` installed. You can achieve this by installing the App `App Installer` from the [Microsoft Store](https://www.microsoft.com/store/productId/9NBLGGH4NNS1).

```PowerShell
# WinGet software
winget install --id 'Git.Git'
winget install --id 'Microsoft.PowerShell'
winget install --id 'Microsoft.AzureCLI'
winget install --id 'Microsoft.Bicep'
winget install --id 'Microsoft.VisualStudioCode'
winget install --id 'GitHub.GitHubDesktop'
winget install --id 'Microsoft.WindowsTerminal'

# VS Code Extensions
code --install-extension 'ms-azuretools.vscode-bicep'
code --install-extension 'ms-vscode.PowerShell'
code --install-extension 'msazurermtools.azurerm-vscode-tools'
code --install-extension 'ms-vscode.azurecli'

# Installing or updating PowerShell modules may require elevated permissions.
Install-Module -Name 'Az.Accounts' -Scope 'CurrentUser' -Force
Install-Module -Name 'Az.Resources' -Scope 'CurrentUser' -Force
Install-Module -Name 'Pester' -Scope 'CurrentUser' -Force
```

If you already have the Azure CLI installed, please update your CLI installation with `az upgrade`.  
You should also update the PowerShell modules, if installed, with `Update-Module`.

</details>

<details>
<summary><b>Check for prefix availability (Lab 2)</b></summary>

To check the availability of the name-prefix you must choose in Lab 2, please copy and paste the below snippet into a PowerShell window and provide a name for the requested `namePrefix` parameter.

```PowerShell
function Test-NamePrefixAvailability {

    param(
        [Parameter(Mandatory = $true)]
        [string] $namePrefix
    )

    if(-not (Get-AzContext)) {
      Write-Warning "In order to execute this function you must be logged into Azure. Initiating"
      Connect-AzAccount
    }

    $prefixAvailable = $true

    # Build names
    $storageAccountNames = @(
      ('adp{0}azsafa001' -f $namePrefix),
      ('{0}azsax001' -f $namePrefix)
    )
    $keyVaultNames = @(
      ('adp-{0}-az-kv-x-001' -f $namePrefix),
      ('{0}-az-kvlt-x-001' -f $namePrefix)
    )
    $acrNames = @(
      ('adp{0}azacrx001' -f $namePrefix)
    )

    # $accessToken = Get-AzAccessToken

    $subscriptionId = (Get-AzContext).Subscription.Id

    # Storage
    Write-Host "`nCheck Storage Accounts" -ForegroundColor 'Cyan'
    Write-Host '======================' -ForegroundColor 'Cyan'
    foreach ($storageAccountName in $storageAccountNames) {
        $path = '/subscriptions/{0}/providers/Microsoft.Storage/checkNameAvailability?api-version=2021-04-01' -f $subscriptionId
        $requestInputObject = @{
            Method  = 'POST'
            Path    = $path
            Payload = @{
                name = $storageAccountName
                type = 'Microsoft.Storage/storageAccounts'
            } | ConvertTo-Json
        }
        $response = ((Invoke-AzRestMethod @requestInputObject).Content | ConvertFrom-Json)
        if ($response.nameAvailable) { Write-Host "=> The storage account name [$storageAccountName] is currently available" -ForegroundColor 'Green' }
        else {
            Write-Warning "=> Warning: The storage account name [$storageAccountName] is not available. Please try a different prefix."
            $prefixAvailable = $false
        }
    }

    # Key Vault
    Write-Host "`nChecking Key Vaults" -ForegroundColor 'Cyan'
    Write-Host '===================' -ForegroundColor 'Cyan'
    foreach ($keyVaultName in $keyVaultNames) {
        $path = '/subscriptions/{0}/providers/Microsoft.KeyVault/checkNameAvailability?api-version=2021-10-01' -f $subscriptionId
        $requestInputObject = @{
            Method  = 'POST'
            Path    = $path
            Payload = @{
                name = $keyVaultName
                type = 'Microsoft.KeyVault/vaults'
            } | ConvertTo-Json
        }
        $response = ((Invoke-AzRestMethod @requestInputObject).Content | ConvertFrom-Json)
        if ($response.nameAvailable) { Write-Host "=> The key vault name [$keyVaultName] is currently available" -ForegroundColor 'Green' }
        else {
            Write-Warning "=> Warning: The key vault name [$keyVaultName] is not available. Please try a different prefix."
            $prefixAvailable = $false
        }
    }

    # Azure Container Registry
    Write-Host "`nCheck Azure Container Registies" -ForegroundColor 'Cyan'
    Write-Host '==============================' -ForegroundColor 'Cyan'
    foreach ($acrName in $acrNames) {
        $path = '/subscriptions/{0}/providers/Microsoft.ContainerRegistry/checkNameAvailability?api-version=2019-05-01' -f $subscriptionId
        $requestInputObject = @{
            Method  = 'POST'
            Path    = $path
            Payload = @{
                name = $acrName
                type = 'Microsoft.ContainerRegistry/registries'
            } | ConvertTo-Json
        }
        $response = ((Invoke-AzRestMethod @requestInputObject).Content | ConvertFrom-Json)
        if ($response.nameAvailable) { Write-Host "=> The Azure container registry name [$acrName] is currently available" -ForegroundColor 'Green' }
        else {
            Write-Warning "=> Warning: The Azure container registry name [$acrName] is not available. Please try a different prefix."
            $prefixAvailable = $false
        }
    }

    Write-Host "`nRESULT" -ForegroundColor 'Cyan'
    Write-Host '======' -ForegroundColor 'Cyan'
    if (-not $prefixAvailable) {
        Write-Error "=> Prefix [$namePrefix] is not available for all resources. Please try a different one."
    }
    else {
        Write-Host "=> Prefix [$namePrefix] is available for all resources." -ForegroundColor 'Green'
    }
}
Test-NamePrefixAvailability
```

</details>