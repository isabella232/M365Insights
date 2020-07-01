# Set up Azure Data Factory to Extract OData Feed from Workplace Analytics to Blob Storage

[![Deploy to Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fnk-gears%2Fwpa-adf-blob-feed%2Fmaster%2Ftemplate.json)


This document explains on how to set up an Azure data factory to access query data from Workplace Analytics and copy it to a blob storage by using an Azure Resource Manager template with PowerShell script.

**Prerequisites**

- Microsoft Azure subscription
  - If you do not have one, you can get one (for free) at [https://azure.microsoft.com/free](https://azure.microsoft.com/free/)
  - The account you use to sign in must have the **global administrator** role granted.

- PowerShell 7.0 to run the scripts.
- Alternatively, you can also use Azure Shell in Azure to run these scripts.

## Set up your environment

1. Install AzureRM powershell module (If you already have it installed, skip to the next step.)
   - Documentation: https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps
   - Open up PowerShell ISE or PowerShell and run the following:
     - `Install-Module -Name Az -AllowClobber -Scope CurrentUser`
2. Login to your Azure Subscription
   - If you don't have an Azure account, sign up for free at https://azure.microsoft.com/en-us/free/
   - And then from PowerShell run: `Connect-AzAccount`
   - If you have multiple subscriptions, you will need to select the one you want to use:
     - `Select-AzureAzSubscription`

The following sections explains how this will be achieved.


## Configuration variables

```
# PowerShell input prompts
- Subscription Id - Use this for WPA: bc85080a-0c4a-41ba-8b88-add5d6714c4b
- Resource Group Name
- AD App Registration Name

# Default values
- Resource group location defaulted to *eastus*. Use PowerShell to change the default or add a new location to create a new Resource group. 

```

### Template parameters

App Specific
- wpaReaderAppSecretName

Vault Specific
- wpaKeyVaultName
- skipVaultCreation (to reuse existing in the same Resource group)

Storage & ADF Specific
- wpaAppStorageAccType
- wpaAppStorageAccNamePrefix
- wpaAppDataFactoryName
- wpaADFJobName   e.g. PersonEmailStats
- wpaEntityName   e.g. Persons or Meetings
- wpaSourceODataFeedUrl

**FAQs**

**Q1. Can I use an Azure Resource Manager template (such as DeployTemplate through the UI) to run all the steps instead of PowerShell?**

- PowerShell is required for this initial setup. You must use PowerShell to create the Active Directory with Service Principal, register the Active Directory application, and automatically create secrets.
- You can skip PowerShell for the subsequent incremental deployments after completing this initial setup.

**Q2. Can I deploy multiple Azure data factories or multiple pipelines with the same data factory and Resource group?**

Yes, you can use the new "wpaADFJobName" parameter that the initial setup adds to create new pipelines.

## Folder Structure

```
-- template.json
-- template-params.json
-- adf-wpa-feed-deploy.ps1
-- adf-wpa-destroy.ps1
-- show-parameters.ps1

```

### Deploy the template

Please edit the variables before deploying

Deploy the template using the PowerShell ISE (Hit F5) or with PowerShell: 
`.\adf-wpa-feed-deploy.ps1`

### Destroy the resources

`.\adf-wpa-destroy.ps1`

### Note

```
The current OData WPA service works like a limited dataset. Dynamic query data is not currently supported.

The following is an example of an OData public service that you can use $select and $top with: 
`https://services.odata.org/v4/(S(34wtn2c0hkuk5ekg0pjr513b))/TripPinServiceRW/People?$top=1` 

```

