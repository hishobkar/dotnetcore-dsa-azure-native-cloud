# Task 1: Introduction to Azure Resource Manager (ARM)

## Overview
This task provides an introduction to Azure Resource Manager (ARM), the deployment and management service for Azure that enables you to create, update, and delete resources in your Azure account.

## Prerequisites
- Active Azure subscription
- Azure CLI or Azure PowerShell installed
- Basic understanding of cloud concepts
- Visual Studio or Visual Studio Code with .NET Core SDK installed
- Azure account with appropriate permissions

## Learning Objectives
By the end of this task, you should be able to:
1. Understand the fundamentals of Azure Resource Manager
2. Comprehend ARM architecture and its components
3. Learn about resource groups and their importance
4. Understand ARM templates and their structure
5. Deploy resources using ARM in a .NET Core application

## What is Azure Resource Manager (ARM)?

Azure Resource Manager is the deployment and management service for Azure. It provides a management layer that enables you to create, update, and delete resources in your Azure subscription. ARM provides a consistent management layer for all operations, whether you use the Azure portal, Azure PowerShell, Azure CLI, REST APIs, or client SDKs.

### Key Benefits of ARM
- **Template-based deployment**: Deploy, manage, and monitor resources as a group
- **Declarative syntax**: Define infrastructure using JSON templates
- **Idempotent operations**: Re-run deployments without affecting existing resources
- **Dependency management**: ARM handles resource dependencies automatically
- **Access control**: Built-in RBAC (Role-Based Access Control)
- **Tagging**: Organize resources logically with tags
- **Billing**: View costs by tags and resource groups

## ARM Architecture Components

### 1. Resource
An individual manageable item available through Azure (e.g., virtual machine, storage account, web app, database).

### 2. Resource Group
A container that holds related resources for an Azure solution. Acts as a logical container for resources that share the same lifecycle.

### 3. Resource Provider
A service that supplies Azure resources. Examples:
- `Microsoft.Compute` - Virtual machines
- `Microsoft.Storage` - Storage accounts
- `Microsoft.Web` - Web apps
- `Microsoft.Sql` - SQL databases
- `Microsoft.KeyVault` - Key vaults

### 4. ARM Template
A JSON file that defines the infrastructure and configuration for your Azure deployment (Infrastructure as Code).

### 5. Declarative Syntax
Specify what you want to deploy without writing programming commands.

## Resource Groups

### Characteristics
- All resources must belong to a resource group
- Resources can only belong to one resource group
- Resource groups cannot be nested
- Resources can be moved between resource groups
- Resources in a resource group can be in different regions
- Resource group location stores metadata about resources

### Best Practices
- Group resources by lifecycle (deploy, update, delete together)
- Use consistent naming conventions
- Apply tags for organization and billing
- Set appropriate access controls at the resource group level
- Consider environment separation (dev, test, prod)

## ARM Templates

### Template Structure
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {}
}
```

### Template Sections
1. **$schema**: Location of the JSON schema file
2. **contentVersion**: Version of the template
3. **parameters**: Input values for customization
4. **variables**: Values constructed from parameters
5. **resources**: Resource types to deploy
6. **outputs**: Values returned after deployment

## Working with ARM in .NET Core

### NuGet Packages Required
```bash
dotnet add package Azure.Identity
dotnet add package Azure.ResourceManager
dotnet add package Azure.ResourceManager.Resources
dotnet add package Azure.ResourceManager.Compute
dotnet add package Azure.ResourceManager.Storage
```

### Authentication Setup
```csharp
using Azure.Identity;
using Azure.ResourceManager;
using Azure.ResourceManager.Resources;

// Create ArmClient with DefaultAzureCredential
var credential = new DefaultAzureCredential();
var armClient = new ArmClient(credential);
```

### Example: Create Resource Group
```csharp
using Azure;
using Azure.Identity;
using Azure.ResourceManager;
using Azure.ResourceManager.Resources;
using Azure.Core;

namespace ArmIntroduction
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // Authenticate
            var credential = new DefaultAzureCredential();
            var armClient = new ArmClient(credential);
            
            // Get subscription
            string subscriptionId = "your-subscription-id";
            var subscription = armClient.GetSubscriptionResource(
                new ResourceIdentifier($"/subscriptions/{subscriptionId}")
            );
            
            // Create Resource Group
            string resourceGroupName = "myResourceGroup";
            var location = AzureLocation.EastUS;
            
            var resourceGroupData = new ResourceGroupData(location)
            {
                Tags = { { "Environment", "Development" }, { "Project", "ARMIntro" } }
            };
            
            var resourceGroupOperation = await subscription.GetResourceGroups()
                .CreateOrUpdateAsync(
                    WaitUntil.Completed, 
                    resourceGroupName, 
                    resourceGroupData
                );
            
            var resourceGroup = resourceGroupOperation.Value;
            Console.WriteLine($"Resource Group '{resourceGroup.Data.Name}' created successfully.");
        }
    }
}
```

### Example: List Resource Groups
```csharp
// Get all resource groups in subscription
var resourceGroups = subscription.GetResourceGroups().GetAllAsync();

await foreach (var rg in resourceGroups)
{
    Console.WriteLine($"Name: {rg.Data.Name}, Location: {rg.Data.Location}");
}
```

### Example: Deploy ARM Template
```csharp
using Azure.ResourceManager.Resources.Models;
using System.Text.Json;

// Get resource group
var resourceGroup = await subscription.GetResourceGroups()
    .GetAsync("myResourceGroup");

// Read ARM template
string templateContent = File.ReadAllText("template.json");
var templateObject = JsonSerializer.Deserialize<object>(templateContent);

// Create deployment content
var deploymentContent = new ArmDeploymentContent(
    new ArmDeploymentProperties(ArmDeploymentMode.Incremental)
    {
        Template = BinaryData.FromObjectAsJson(templateObject)
    }
);

// Start deployment
string deploymentName = "myDeployment";
var deploymentOperation = await resourceGroup.Value.GetArmDeployments()
    .CreateOrUpdateAsync(
        WaitUntil.Completed,
        deploymentName,
        deploymentContent
    );

Console.WriteLine($"Deployment '{deploymentName}' completed successfully.");
```

## Common ARM Operations in .NET Core

### 1. Create Resources
```csharp
// Example operations structure
var operation = await resourceCollection.CreateOrUpdateAsync(
    WaitUntil.Completed,
    resourceName,
    resourceData
);
```

### 2. Get Resources
```csharp
var resource = await resourceCollection.GetAsync(resourceName);
```

### 3. Update Resources
```csharp
var existingResource = await resourceCollection.GetAsync(resourceName);
// Modify resource data
var updateOperation = await existingResource.Value.UpdateAsync(
    WaitUntil.Completed,
    updatedData
);
```

### 4. Delete Resources
```csharp
var resource = await resourceCollection.GetAsync(resourceName);
await resource.Value.DeleteAsync(WaitUntil.Completed);
```

## Configuration in .NET Core

### appsettings.json
```json
{
  "Azure": {
    "SubscriptionId": "your-subscription-id",
    "TenantId": "your-tenant-id",
    "ClientId": "your-client-id",
    "ClientSecret": "your-client-secret",
    "ResourceGroupName": "myResourceGroup",
    "Location": "eastus"
  }
}
```

### Reading Configuration
```csharp
using Microsoft.Extensions.Configuration;

var configuration = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
    .Build();

string subscriptionId = configuration["Azure:SubscriptionId"];
string resourceGroupName = configuration["Azure:ResourceGroupName"];
```

## Authentication Methods

### 1. DefaultAzureCredential (Recommended)
```csharp
var credential = new DefaultAzureCredential();
```

### 2. Client Secret Credential
```csharp
var credential = new ClientSecretCredential(tenantId, clientId, clientSecret);
```

### 3. Interactive Browser Credential
```csharp
var credential = new InteractiveBrowserCredential();
```

## Best Practices

1. **Use Managed Identities** when running in Azure
2. **Store secrets securely** using Azure Key Vault
3. **Use resource tags** for organization and cost tracking
4. **Implement proper error handling** with try-catch blocks
5. **Use async/await** for all Azure operations
6. **Follow naming conventions** for resources
7. **Use ARM templates** for repeatable deployments
8. **Implement least privilege access** with RBAC
9. **Use resource locks** to prevent accidental deletion
10. **Monitor deployments** and set up alerts

## Error Handling Example
```csharp
try
{
    var resourceGroupOperation = await subscription.GetResourceGroups()
        .CreateOrUpdateAsync(
            WaitUntil.Completed,
            resourceGroupName,
            resourceGroupData
        );
    
    Console.WriteLine("Resource group created successfully.");
}
catch (RequestFailedException ex)
{
    Console.WriteLine($"Azure request failed: {ex.Message}");
    Console.WriteLine($"Error Code: {ex.ErrorCode}");
    Console.WriteLine($"Status: {ex.Status}");
}
catch (Exception ex)
{
    Console.WriteLine($"An error occurred: {ex.Message}");
}
```

## Hands-On Exercise

### Exercise 1: Create a Simple .NET Core Console Application

1. Create a new .NET Core console application
```bash
dotnet new console -n ArmIntroduction
cd ArmIntroduction
```

2. Add required NuGet packages
```bash
dotnet add package Azure.Identity
dotnet add package Azure.ResourceManager
dotnet add package Azure.ResourceManager.Resources
```

3. Implement resource group creation and listing functionality

4. Add proper error handling and logging

5. Test the application with your Azure subscription

### Exercise 2: Deploy a Simple ARM Template

1. Create a simple ARM template for a storage account
2. Use the .NET Core SDK to deploy the template
3. Verify the deployment in Azure Portal

## Additional Resources

- [Azure Resource Manager Documentation](https://docs.microsoft.com/azure/azure-resource-manager/)
- [Azure SDK for .NET Documentation](https://docs.microsoft.com/dotnet/azure/)
- [ARM Template Reference](https://docs.microsoft.com/azure/templates/)
- [Azure.ResourceManager NuGet Package](https://www.nuget.org/packages/Azure.ResourceManager/)
- [Azure Samples on GitHub](https://github.com/Azure-Samples)

## Summary

In this task, you learned:
- The fundamentals of Azure Resource Manager
- ARM architecture and its key components
- How to work with resource groups
- How to use the Azure SDK for .NET Core
- How to perform basic ARM operations programmatically
- Best practices for working with ARM in .NET Core applications
