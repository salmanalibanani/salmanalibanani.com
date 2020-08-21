---
title: "Pushing Secrets Into Azure Key Vault Directly From ARM Template"
date: 2020-07-14T23:20:56+10:00
draft: false
tags: ["Azure", "DevOps", "Key Vault"]
summary: "Another way to push secrets from Azure DevOps to a Key Vault using an ARM template's output."
---
<a href="https://docs.microsoft.com/en-us/azure/key-vault/general/" target="_blank">Azure Key Vault</a> is a service that you can use to store application secrets and keys. You can store tokens, connection strings, certificates, API keys etc. in a Key Vault, and it provides a secured location with all usual safegaurds (encryption, access control etc.) that you would expect from such a solution.

In a previos <a href="/2020/07/09/storing-secrets-in-azure-key-vault-from-azure-devops-release-pipeline/" target="_blank">post</a>, I  showed a simple technique to store the access credentials of a resource (e.g. a Service Bus) in an Azure Key Vault. In that post, I used “Write Secrets” task in an Azure Release pipeline to store the credentials.

Below I show a somewhat different way to do this and keep the pipeline simple by writing the secret directly from ARM template that creates the resource (i.e. Service Bus) in previous step.

Have a look at <a href="https://github.com/salmanalibanani/AzureKeyVaultFromTemplate" target="_blank">this</a> repository for an example of this approach. I have used a YAML based pipeline to deploy various resources (Resource Group, Key Vault, Service Bus), and our objective is to store the Servie Bus endpoint information in the Key Vault (which is created one step before the Service Bus in the pipeline). The important bit here is last part of the template that deploys Service Bus.
<pre><code>{
    &quot;type&quot;: &quot;Microsoft.KeyVault/vaults/secrets&quot;,
    &quot;name&quot;: &quot;[concat(parameters(&apos;keyVaultName&apos;), &apos;/ServiceBusConnectionString&apos;)]&quot;,
    &quot;apiVersion&quot;: &quot;2019-09-01&quot;,
    &quot;dependsOn&quot;: [
        &quot;[parameters(&apos;serviceBusNamespaceName&apos;)]&quot;
    ],
    &quot;location&quot;: &quot;[parameters(&apos;location&apos;)]&quot;, 
    &quot;properties&quot;: {
        &quot;value&quot;: &quot;[listkeys(variables(&apos;authRuleResourceId&apos;), variables(&apos;sbVersion&apos;)).primaryConnectionString]&quot;
    }
}</code></pre>

This creates a new secret “ServiceBusConnectionString” in the Key Vault whose name is provided as a parameter to the template.

Note that to make this sample work, you will have to do a few things:

1. Define pipeline variables **azureResourceManagerConnectionName**, and **SubscriptionId** that I have used in my YAML pipeline definition.
2. Set objectId in accessPolicies in the parameters file for the template that deploys Key Vault. This objectId is supposed to be the id of the user in your Azure Active Direct tenant who will have access to this Key Vault. You will notice that in the **keysPermission** variable in our template, we have given list permissions to this user. You can use the **Get-AzureRmADUser** in Azure Cloud Shell to find an Id of a user that you want to give access to this Key Vault.

![New release pipeline](/img/pushing-secrets-into-azure-key-vault-directly-from-arm-template/objectid.png)

This approach clearly makes the pipeline simpler, with no need to inject a script to parse template outputs to extract the secret (Service Bus endpoint in this case).

## Conclusion
In this post we learned how to store credentials to access an Azure resource directly from an ARM template that deploys that resource, without parsing template output and using the “Writing Secrets” task in a Release pipeline.
