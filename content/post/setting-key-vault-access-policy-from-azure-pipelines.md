---
title: "Setting Key Vault Access Policy from Azure Pipelines"
date: 2020-11-20T10:51:02+11:00
draft: false 
tags: ["Azure", "Key Vault", "Pipelines"]
summary: "How to set access policy on a Key Vault created via an ARM template."
---

<a href="https://docs.microsoft.com/en-us/azure/key-vault/general/" target="_blank">Azure Key Vault</a> is a service that can be used to store application secrets and keys. You can store tokens, connection strings, certificates, API keys etc. in a Key Vault, and it provides a secured location with all usual safegaurds (encryption, access control etc.)

If you create a Key Vault via an ARM template in your YAML base pipeline, you need to set access policy on that Key Vault, so that in later stages of the pipeline you can store secrets in it.  You have to find an Azure AD Service Principal that Azure Dev Ops uses to access your Azure resources.  Below we will show how to do that.

## The problem

Below is an example run of Azure Pipelines where you can see a number of resources being created (Resource Group, Service Bus etc).  Our objective in this pipeline is to store the connection information for the Azure Service Bus in Key Vault.  The connection information for Service Bus comes from the output of the ARM template that creates Service Bus.  You can find this pipeline in the repository linked below.

![Problem](/img/setting-key-vault-access-policy-from-azure-pipelines/problem.jpg)

The problem here is that even though this particular Key Vault is created in the same pipeline, the later stages cannot write secrets to it because, as the error suggests, we don't seem to have permissions to set secrets on that Key Vault.

As shown below in the snippet from the ARM template, we can set access policies if we can find out the objectId and tennantId.  Note how we have set all permissions on "secrets".

```code

"resources": [
    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2019-09-01",
        "name": "[parameters('keyVaultName')]",
        "location": "[parameters('location')]",
        "properties": {
            "enabledForDeployment": "[parameters('enabledForDeployment')]",
            "enabledForDiskEncryption": "[parameters('enabledForDiskEncryption')]",
            "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
            "tenantId": "[parameters('tenantId')]",
            "accessPolicies": [
                {
                    "objectId": "[parameters('objectId')]",
                    "tenantId": "[parameters('tenantId')]",
                    "permissions": {
                        "secrets": [ "all" ]
                    }
                }
            ],
            "sku": {
                "name": "[parameters('skuName')]",
                "family": "A"
            },
            "networkAcls": {
                "defaultAction": "Allow",
                "bypass": "AzureServices"
            }
        }
    }
]
```
Below are the steps to find out these ids.

## Step 1 - Find Service Principal details for your Azure Dev Ops project.

In your Azure Dev Ops projects where your Azure Pipelines pipeline resides, click on "Service connections" in Project Settings, and then click on "Manage Service Principal" link.

![Problem](/img/setting-key-vault-access-policy-from-azure-pipelines/Step1.jpg)

This takes you to a page where you can find application id for your Azure Dev Ops project.

![Problem](/img/setting-key-vault-access-policy-from-azure-pipelines/Step1A.jpg)

## Step 2 - Find Azure AD Service Principal
Using this application id, we need to find the Azure AD Service Principal object id that we can use in our ARM template for the Key Vault.  

Open Cloud Shell in your Azure portal, and type the following:

```code
Connect-AzureAD
Get-AzureADServicePrincipal -Filter "AppId eq '95fc81ef-589e-4638-ba7c-74fb52fed6e8'"
```
You can see the ObjectId in the result that you need.

![Problem](/img/setting-key-vault-access-policy-from-azure-pipelines/Step2.jpg)

## Step 3 - Use the Object Id in your ARM template parameters file

Now we have all the information that we need to pass as parameters to our ARM template.  Here is our parameters.json file for the template.

```code
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultName": {
            "value": "aikv2034"
        },
        "tenantId": {
            "value": "051137fd-71a2-4ec0-88bc-e7db221e4fa3"
        },
        "objectId": {
            "value": "5c8fb9bf-3b0a-4448-b06d-2fdab082e7c6"
        }
    }
}
```
The tenantId comes from the screenshot in Step 1 above, and objectId is the id that we found using Cloud Sell in step 2.

Now if you run the pipeline again, you will see that the "WriteSecrets" step is successful, and the connection string for the Service Bus is being saved successfully.

![Success](/img/setting-key-vault-access-policy-from-azure-pipelines/Success.jpg)

## How "WriteSecrets" step works

There are a few details in our pipeline .yml file that you may want to have a closer look.

```code
- task: AzureResourceManagerTemplateDeployment@3
  name: DeployServiceBus
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'Visual Studio Professional(47df455b-f3c1-4e50-a88d-ff6a59939ec0)'
    subscriptionId: '47df455b-f3c1-4e50-a88d-ff6a59939ec0'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'AzureIntegrationDemoRG'
    location: 'Australia East'
    templateLocation: 'Linked artifact'
    csmFile: '$(System.DefaultWorkingDirectory)/Templates/ServiceBus/template.json'
    csmParametersFile: '$(System.DefaultWorkingDirectory)/Templates/ServiceBus/template.parameters.json'
    deploymentMode: 'Incremental'
    deploymentOutputs: servicebustemplateoutput
- pwsh: |
    $armOutputObj = '$(servicebustemplateoutput)' | convertfrom-json
    $connectionString = $armOutputObj.namespaceConnectionString.value
    Write-Output "##vso[task.setvariable variable=ServiceBusConnectionString;]$connectionString"
- task: cboroson-WriteSecrets@1
  displayName: WriteSecrets
  inputs:
    ConnectedServiceName: 'Visual Studio Professional(47df455b-f3c1-4e50-a88d-ff6a59939ec0)'
    resourceGroupName: 'AzureIntegrationDemoRG'
    KeyVaultName: 'aikv2034'
    SecretName: 'ServiceBusConnectionString'
    SecretValue: '$(ServiceBusConnectionString)'
```
First, notice the "deploymentOutputs" part in the "DeployServiceBus" task.  This creates a pipeline variable "servicebustemplateoutput" that contains output of the ARM template that creates Service Bus.  We refer to this variable in the next step where we run a custom PowerShell script to parse the connection string out of the json output of the template, and put it in another pipeline variable "ServiceBusConnectionString".

The last bit of the .yml file uses a task to write this connection string as a secret to the Key Vault.

## Conclusion
In this post, we demonstrated how to store credentials to access an Azure resource (Service Bus in this example) in a Key Vault from an Azure Pipeline.  Both the target resource and the Key Vault were created using ARM templates within the same pipeline.  The important step was to find the Service Principal information that we needed to add to our Key Vault ARM template, so that later stages of our pipeline can write to the Key Vault.





