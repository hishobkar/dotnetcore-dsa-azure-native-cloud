# Task 5: Validate, Export, and Monitor ARM Deployments

## Overview
This task focuses on validating ARM template deployments before execution, exporting templates from existing Azure resources for reuse, and monitoring deployment history and status. You'll learn validation techniques, template export methods, and deployment monitoring using Azure Portal, Azure CLI, and .NET Core applications.

## Prerequisites
- Active Azure subscription
- Completed Task 1-4 (ARM Template fundamentals)
- Azure CLI installed
- .NET Core SDK 6.0 or later
- Visual Studio Code
- At least one deployed resource in Azure
- Basic understanding of ARM templates and deployment operations

## Estimated Time
**30 minutes**

## Learning Objectives
By the end of this task, you should be able to:
1. Validate ARM templates before deployment
2. Use validation tools and techniques
3. Export ARM templates from existing resources
4. Customize and reuse exported templates
5. Monitor deployment progress and status
6. View deployment history and logs
7. Troubleshoot failed deployments
8. Implement validation and monitoring in .NET Core
9. Use What-If deployments to preview changes
10. Understand deployment operations and events

## Part 1: Template Validation Fundamentals

### Why Validate Templates?

**Benefits of Validation:**
- Catch syntax errors before deployment
- Verify parameter values are within constraints
- Check resource dependencies
- Validate API versions
- Ensure proper resource configurations
- Save time and prevent failed deployments
- Reduce Azure costs from failed deployments

### Types of Validation

| Validation Type | Description | When to Use |
|----------------|-------------|-------------|
| **Syntax Validation** | Checks JSON structure and ARM syntax | Always, before deployment |
| **Template Validation** | Validates template against Azure schema | Before every deployment |
| **What-If Validation** | Preview changes without deploying | Before production changes |
| **Pre-flight Validation** | Azure validates during deployment | Automatic with deployment |
| **Policy Validation** | Check against Azure Policy | Governance compliance |

### Validation Workflow
```
1. Syntax Check (JSON validity)
   ↓
2. Schema Validation (ARM template structure)
   ↓
3. Template Validation (Azure validates parameters/resources)
   ↓
4. What-If Analysis (Preview changes)
   ↓
5. Deployment (If validation passes)
   ↓
6. Monitor Deployment
   ↓
7. Review Deployment History
```

## Part 2: Validating ARM Templates Using Azure Portal

### Step-by-Step: Portal Validation

#### Step 1: Navigate to Custom Deployment
1. Sign in to Azure Portal (https://portal.azure.com)
2. Search for **"Deploy a custom template"**
3. Click **"Deploy a custom template"**

#### Step 2: Load Template
1. Click **"Build your own template in the editor"**
2. Paste your ARM template JSON
3. Click **"Save"**

#### Step 3: Configure Parameters
1. Fill in required parameters
2. Select subscription and resource group
3. Review all settings

#### Step 4: Validate Without Deploying
1. **Do NOT click "Create" yet**
2. Look for validation messages at the top
3. Check for any warning or error indicators
4. If errors exist, they will be displayed immediately

#### Common Validation Errors in Portal

| Error | Cause | Solution |
|-------|-------|----------|
| "Template validation failed" | Syntax error in JSON | Check JSON formatting |
| "Parameter X is required" | Missing required parameter | Provide parameter value |
| "InvalidTemplateDeployment" | Resource configuration issue | Review resource properties |
| "ResourceNotFound" | Dependency missing | Check dependsOn clauses |
| "AccountNameInvalid" | Naming convention violated | Fix resource name |

### Using the Template Validator

The Azure Portal automatically validates templates when you:
- Load a template in the custom deployment editor
- Click "Review + create"
- Attempt to deploy

**Portal Validation Features:**
- Real-time syntax checking
- Parameter validation
- Schema compliance
- Error highlighting
- Suggested fixes

## Part 3: Validating ARM Templates Using Azure CLI

### Basic Validation Command
```bash
# Validate template with parameter file
az deployment group validate \
  --resource-group myResourceGroup \
  --template-file template.json \
  --parameters parameters.json

# Validate with inline parameters
az deployment group validate \
  --resource-group myResourceGroup \
  --template-file template.json \
  --parameters storageAccountName=mystorageaccount location=eastus

# Validate subscription-level deployment
az deployment sub validate \
  --location eastus \
  --template-file subscription-template.json \
  --parameters parameters.json
```

### Complete Validation Example

Create a validation script `validate-template.sh`:
```bash
#!/bin/bash

# Variables
RESOURCE_GROUP="rg-validation-demo"
LOCATION="eastus"
TEMPLATE_FILE="storage-account.json"
PARAMETERS_FILE="storage-account.parameters.json"

echo "=========================================="
echo "ARM Template Validation Script"
echo "=========================================="

# Check if resource group exists
echo "Checking if resource group exists..."
if az group exists --name $RESOURCE_GROUP | grep -q "true"; then
    echo "✓ Resource group exists"
else
    echo "Creating resource group..."
    az group create --name $RESOURCE_GROUP --location $LOCATION
    echo "✓ Resource group created"
fi

# Validate JSON syntax
echo ""
echo "Validating JSON syntax..."
if jq empty $TEMPLATE_FILE 2>/dev/null; then
    echo "✓ Template JSON is valid"
else
    echo "✗ Template JSON is invalid"
    exit 1
fi

if jq empty $PARAMETERS_FILE 2>/dev/null; then
    echo "✓ Parameters JSON is valid"
else
    echo "✗ Parameters JSON is invalid"
    exit 1
fi

# Validate ARM template
echo ""
echo "Validating ARM template..."
VALIDATION_RESULT=$(az deployment group validate \
  --resource-group $RESOURCE_GROUP \
  --template-file $TEMPLATE_FILE \
  --parameters $PARAMETERS_FILE \
  2>&1)

if [ $? -eq 0 ]; then
    echo "✓ Template validation passed"
    echo ""
    echo "Validation Details:"
    echo "$VALIDATION_RESULT" | jq '.properties'
else
    echo "✗ Template validation failed"
    echo ""
    echo "Error Details:"
    echo "$VALIDATION_RESULT"
    exit 1
fi

# What-If analysis
echo ""
echo "Running What-If analysis..."
az deployment group what-if \
  --resource-group $RESOURCE_GROUP \
  --template-file $TEMPLATE_FILE \
  --parameters $PARAMETERS_FILE

echo ""
echo "=========================================="
echo "Validation completed successfully!"
echo "=========================================="
```

### What-If Deployment Analysis
```bash
# What-If for resource group deployment
az deployment group what-if \
  --resource-group myResourceGroup \
  --template-file template.json \
  --parameters parameters.json

# What-If with result format
az deployment group what-if \
  --resource-group myResourceGroup \
  --template-file template.json \
  --parameters parameters.json \
  --result-format FullResourcePayloads

# What-If for subscription deployment
az deployment sub what-if \
  --location eastus \
  --template-file template.json \
  --parameters parameters.json
```

### Understanding What-If Output
```plaintext
Resource changes: 3 to create, 1 to modify, 0 to delete, 2 no change

Scope: /subscriptions/{subscription-id}/resourceGroups/myResourceGroup

  + Microsoft.Storage/storageAccounts/mystorageaccount [2023-01-01]
      apiVersion:                     "2023-01-01"
      kind:                           "StorageV2"
      location:                       "eastus"
      name:                           "mystorageaccount"
      
  ~ Microsoft.Web/sites/mywebapp [2022-03-01]
    ~ properties.httpsOnly:          false => true
    ~ properties.siteConfig.netFrameworkVersion: "v4.8" => "v6.0"
```

**Symbol Legend:**
- `+` Create new resource
- `~` Modify existing resource
- `-` Delete resource
- `*` Deploy (no change)
- `!` Ignore

## Part 4: Exporting ARM Templates from Existing Resources

### Export from Azure Portal

#### Method 1: Export from Resource

1. Navigate to any existing resource (e.g., Storage Account)
2. In the left menu, find **"Export template"** under Automation section
3. Review the generated template
4. Options:
   - **Download**: Get JSON files locally
   - **Add to library**: Save to template library
   - **Deploy**: Redeploy the template

#### Method 2: Export from Resource Group

1. Go to your Resource Group
2. Click **"Export template"** in the left menu
3. Select resources to include (or select all)
4. Click **"Download"** or **"Add to library"**

#### Method 3: Export from Deployment History

1. Navigate to Resource Group
2. Click **"Deployments"** in the left menu
3. Select a deployment
4. Click **"Template"** tab
5. Download or view the template

### Export Using Azure CLI
```bash
# Export single resource
az resource show \
  --resource-group myResourceGroup \
  --name myStorageAccount \
  --resource-type Microsoft.Storage/storageAccounts \
  --query properties

# Export entire resource group
az group export \
  --name myResourceGroup \
  --output json > exported-template.json

# Export specific resources from resource group
az group export \
  --name myResourceGroup \
  --resource-ids \
    "/subscriptions/{sub-id}/resourceGroups/myRG/providers/Microsoft.Storage/storageAccounts/storage1" \
    "/subscriptions/{sub-id}/resourceGroups/myRG/providers/Microsoft.Web/sites/webapp1" \
  --output json > exported-template.json

# Export with parameters included
az group export \
  --name myResourceGroup \
  --include-parameter-default-value \
  --output json > exported-template.json
```

### Cleaning Up Exported Templates

Exported templates often need cleanup:

**Common Issues:**
1. Hard-coded values instead of parameters
2. Unnecessary properties
3. Resource IDs instead of names
4. Comments field with export metadata
5. Missing parameter defaults

**Example: Raw Exported Template**
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2023-01-01",
      "name": "mystorageaccount12345",
      "location": "eastus",
      "tags": {},
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2",
      "properties": {
        "dnsEndpointType": "Standard",
        "defaultToOAuthAuthentication": false,
        "publicNetworkAccess": "Enabled",
        "allowCrossTenantReplication": true,
        "minimumTlsVersion": "TLS1_2",
        "allowBlobPublicAccess": true,
        "allowSharedKeyAccess": true,
        "networkAcls": {
          "bypass": "AzureServices",
          "virtualNetworkRules": [],
          "ipRules": [],
          "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": true,
        "encryption": {
          "requireInfrastructureEncryption": false,
          "services": {
            "file": {
              "keyType": "Account",
              "enabled": true
            },
            "blob": {
              "keyType": "Account",
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "accessTier": "Hot"
      }
    }
  ]
}
```

**Example: Cleaned Template**
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage account name"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "sku": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Premium_LRS"
      ]
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2023-01-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('sku')]"
      },
      "kind": "StorageV2",
      "properties": {
        "supportsHttpsTrafficOnly": true,
        "minimumTlsVersion": "TLS1_2",
        "encryption": {
          "services": {
            "file": {
              "enabled": true
            },
            "blob": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "accessTier": "Hot"
      }
    }
  ]
}
```

## Part 5: Monitoring ARM Deployments

### Monitor Deployment in Azure Portal

#### Real-Time Deployment Monitoring

1. After starting a deployment, click **"Go to deployment"**
2. View deployment status:
   - Running
   - Succeeded
   - Failed
   - Canceled

3. Monitor progress:
   - Overall deployment status
   - Individual resource status
   - Deployment duration
   - Resource creation order

#### Deployment Details View

**Overview Tab:**
- Deployment name
- Status
- Resource group
- Subscription
- Timestamp
- Duration
- Correlation ID

**Template Tab:**
- View deployed template
- View parameters used
- Download template

**Inputs Tab:**
- Parameter values provided
- Template link (if used)

**Outputs Tab:**
- Output values returned
- Useful for getting created resource IDs, URLs, etc.

**Operations Tab:**
- Detailed operation list
- Each resource creation step
- Duration per operation
- Status per operation

### Viewing Deployment History

#### In Resource Group

1. Navigate to your Resource Group
2. Click **"Deployments"** in the left menu
3. View all historical deployments:
   - Deployment name
   - Status
   - Timestamp
   - Duration
   - Initiated by

4. Filter deployments:
   - By date range
   - By status (succeeded, failed)
   - By deployment name

#### Deployment Operations

1. Click on any deployment
2. Go to **"Operations"** or **"Operation details"**
3. View detailed steps:
```
Operation Name                          Status      Duration
Microsoft.Resources/deployments         Succeeded   2m 15s
  Microsoft.Storage/storageAccounts     Succeeded   45s
  Microsoft.Web/serverfarms             Succeeded   30s
  Microsoft.Web/sites                   Succeeded   1m 00s
```

### Monitor Using Azure CLI
```bash
# List deployments in resource group
az deployment group list \
  --resource-group myResourceGroup \
  --output table

# Get specific deployment details
az deployment group show \
  --name myDeployment \
  --resource-group myResourceGroup

# Show deployment operations
az deployment operation group list \
  --name myDeployment \
  --resource-group myResourceGroup

# Watch deployment in real-time (subscription level)
az deployment sub create \
  --location eastus \
  --template-file template.json \
  --parameters parameters.json \
  --name myDeployment \
  --verbose

# Get deployment logs
az deployment group show \
  --name myDeployment \
  --resource-group myResourceGroup \
  --query properties.error

# List failed deployments
az deployment group list \
  --resource-group myResourceGroup \
  --filter "provisioningState eq 'Failed'" \
  --output table
```

### Activity Log Monitoring
```bash
# View activity log for resource group
az monitor activity-log list \
  --resource-group myResourceGroup \
  --start-time 2024-01-01T00:00:00Z \
  --output table

# Filter by deployment operations
az monitor activity-log list \
  --resource-group myResourceGroup \
  --caller "Microsoft.Resources/deployments" \
  --output json

# Get logs for specific deployment
az monitor activity-log list \
  --correlation-id {correlation-id} \
  --output json
```

## Part 6: .NET Core Implementation

### Project Setup
```bash
# Create console application
dotnet new console -n ArmDeploymentMonitor
cd ArmDeploymentMonitor

# Add required packages
dotnet add package Azure.Identity
dotnet add package Azure.ResourceManager
dotnet add package Azure.ResourceManager.Resources
dotnet add package Microsoft.Extensions.Configuration
dotnet add package Microsoft.Extensions.Configuration.Json
dotnet add package Newtonsoft.Json
```

### Configuration (appsettings.json)
```json
{
  "Azure": {
    "SubscriptionId": "your-subscription-id",
    "TenantId": "your-tenant-id",
    "ClientId": "your-client-id",
    "ClientSecret": "your-client-secret"
  },
  "Deployment": {
    "ResourceGroupName": "rg-validation-demo",
    "Location": "eastus"
  }
}
```

### Complete Implementation (Program.cs)
```csharp
using Azure;
using Azure.Core;
using Azure.Identity;
using Azure.ResourceManager;
using Azure.ResourceManager.Resources;
using Azure.ResourceManager.Resources.Models;
using Microsoft.Extensions.Configuration;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

namespace ArmDeploymentMonitor
{
    class Program
    {
        private static IConfiguration _configuration;
        private static ArmClient _armClient;
        private static SubscriptionResource _subscription;
        private static ResourceGroupResource _resourceGroup;

        static async Task Main(string[] args)
        {
            try
            {
                Console.WriteLine("=== ARM Template Validation & Monitoring Tool ===\n");

                LoadConfiguration();
                await InitializeAzureClient();

                await RunMenuAsync();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine($"\nError: {ex.Message}");
                Console.ResetColor();
            }
        }

        static void LoadConfiguration()
        {
            _configuration = new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: false)
                .Build();

            Console.WriteLine("✓ Configuration loaded\n");
        }

        static async Task InitializeAzureClient()
        {
            var credential = new DefaultAzureCredential();
            _armClient = new ArmClient(credential);

            string subscriptionId = _configuration["Azure:SubscriptionId"];
            _subscription = _armClient.GetSubscriptionResource(
                new ResourceIdentifier($"/subscriptions/{subscriptionId}")
            );

            string resourceGroupName = _configuration["Deployment:ResourceGroupName"];
            _resourceGroup = await _subscription.GetResourceGroups()
                .GetAsync(resourceGroupName);

            Console.WriteLine($"✓ Connected to: {_subscription.Data.DisplayName}");
            Console.WriteLine($"✓ Resource Group: {_resourceGroup.Data.Name}\n");
        }

        static async Task RunMenuAsync()
        {
            while (true)
            {
                Console.WriteLine("\n=== Main Menu ===");
                Console.WriteLine("1. Validate Template");
                Console.WriteLine("2. Deploy Template");
                Console.WriteLine("3. List Deployments");
                Console.WriteLine("4. Get Deployment Details");
                Console.WriteLine("5. View Deployment Operations");
                Console.WriteLine("6. Export Resource Group Template");
                Console.WriteLine("7. Export Single Resource");
                Console.WriteLine("8. Monitor Active Deployment");
                Console.WriteLine("9. View Failed Deployments");
                Console.WriteLine("10. Exit");
                Console.Write("\nSelect option: ");

                string choice = Console.ReadLine();

                try
                {
                    switch (choice)
                    {
                        case "1":
                            await ValidateTemplateAsync();
                            break;
                        case "2":
                            await DeployTemplateAsync();
                            break;
                        case "3":
                            await ListDeploymentsAsync();
                            break;
                        case "4":
                            await GetDeploymentDetailsAsync();
                            break;
                        case "5":
                            await ViewDeploymentOperationsAsync();
                            break;
                        case "6":
                            await ExportResourceGroupTemplateAsync();
                            break;
                        case "7":
                            await ExportSingleResourceAsync();
                            break;
                        case "8":
                            await MonitorActiveDeploymentAsync();
                            break;
                        case "9":
                            await ViewFailedDeploymentsAsync();
                            break;
                        case "10":
                            Console.WriteLine("Exiting...");
                            return;
                        default:
                            Console.WriteLine("Invalid option");
                            break;
                    }
                }
                catch (RequestFailedException ex)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine($"\nAzure Error: {ex.Message}");
                    Console.WriteLine($"Error Code: {ex.ErrorCode}");
                    Console.WriteLine($"Status: {ex.Status}");
                    Console.ResetColor();
                }
                catch (Exception ex)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine($"\nError: {ex.Message}");
                    Console.WriteLine($"Stack Trace: {ex.StackTrace}");
                    Console.ResetColor();
                }
            }
        }

        // 1. Validate Template
        static async Task ValidateTemplateAsync()
        {
            Console.WriteLine("\n=== Validate Template ===");
            Console.Write("Enter template file path: ");
            string templatePath = Console.ReadLine();

            if (!File.Exists(templatePath))
            {
                Console.WriteLine("Template file not found!");
                return;
            }

            // Load template
            string templateContent = File.ReadAllText(templatePath);
            object templateObject = JsonConvert.DeserializeObject<object>(templateContent);

            // Load parameters (optional)
            Console.Write("Enter parameters file path (or press Enter to skip): ");
            string parametersPath = Console.ReadLine();

            Dictionary<string, object> parameters = null;
            if (!string.IsNullOrWhiteSpace(parametersPath) && File.Exists(parametersPath))
            {
                string parametersContent = File.ReadAllText(parametersPath);
                var parametersJson = JObject.Parse(parametersContent);
                parameters = new Dictionary<string, object>();

                foreach (var param in parametersJson["parameters"])
                {
                    var property = (JProperty)param;
                    parameters[property.Name] = new { value = property.Value["value"] };
                }
            }

            Console.WriteLine("\nValidating template...");

            try
            {
                var deploymentContent = new ArmDeploymentContent(
                    new ArmDeploymentProperties(ArmDeploymentMode.Incremental)
                    {
                        Template = BinaryData.FromObjectAsJson(templateObject),
                        Parameters = parameters != null ? BinaryData.FromObjectAsJson(parameters) : null
                    }
                );

                var validationResult = await _resourceGroup.ValidateArmDeploymentAsync(
                    deploymentContent
                );

                if (validationResult.Value.Error == null)
                {
                    Console.ForegroundColor = ConsoleColor.Green;
                    Console.WriteLine("\n✓ Template validation PASSED");
                    Console.ResetColor();
                    Console.WriteLine($"Correlation ID: {validationResult.Value.Properties.CorrelationId}");
                }
                else
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine("\n✗ Template validation FAILED");
                    Console.ResetColor();
                    Console.WriteLine($"Error Code: {validationResult.Value.Error.Code}");
                    Console.WriteLine($"Message: {validationResult.Value.Error.Message}");

                    if (validationResult.Value.Error.Details != null)
                    {
                        Console.WriteLine("\nDetails:");
                        foreach (var detail in validationResult.Value.Error.Details)
                        {
                            Console.WriteLine($"  - {detail.Code}: {detail.Message}");
                        }
                    }
                }
            }
            catch (RequestFailedException ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine($"\n✗ Validation failed with exception");
                Console.WriteLine($"Error: {ex.Message}");
                Console.ResetColor();
            }
        }

        // 2. Deploy Template
        static async Task DeployTemplateAsync()
        {
            Console.WriteLine("\n=== Deploy Template ===");
            Console.Write("Enter template file path: ");
            string templatePath = Console.ReadLine();

            if (!File.Exists(templatePath))
            {
                Console.WriteLine("Template file not found!");
                return;
            }

            string templateContent = File.ReadAllText(templatePath);
            object templateObject = JsonConvert.DeserializeObject<object>(templateContent);

            Console.Write("Enter deployment name: ");
            string deploymentName = Console.ReadLine();
            if (string.IsNullOrWhiteSpace(deploymentName))
            {
                deploymentName = $"deployment-{DateTime.Now:yyyyMMddHHmmss}";
            }

            Console.WriteLine($"\nDeployment Name: {deploymentName}");
            Console.Write("Proceed with deployment? (yes/no): ");
            if (Console.ReadLine()?.ToLower() != "yes")
            {
                Console.WriteLine("Deployment cancelled");
                return;
            }

            var deploymentContent = new ArmDeploymentContent(
                new ArmDeploymentProperties(ArmDeploymentMode.Incremental)
                {
                    Template = BinaryData.FromObjectAsJson(templateObject)
                }
            );

            Console.WriteLine("\nStarting deployment...");
            var deploymentOperation = await _resourceGroup.GetArmDeployments()
                .CreateOrUpdateAsync(
                    WaitUntil.Started,
                    deploymentName,
                    deploymentContent
                );

            Console.WriteLine("Deployment initiated. Monitoring progress...\n");

            // Monitor deployment
            await MonitorDeploymentProgressAsync(deploymentOperation);
        }

        // 3. List Deployments
        static async Task ListDeploymentsAsync()
        {
            Console.WriteLine("\n=== Deployment History ===");

            var deployments = _resourceGroup.GetArmDeployments().GetAllAsync();

            Console.WriteLine($"\n{"Deployment Name",-40} {"Status",-15} {"Timestamp",-25} {"Duration"}");
            Console.WriteLine(new string('-', 100));

            int count = 0;
            await foreach (var deployment in deployments)
            {
                count++;
                var props = deployment.Data.Properties;
                var duration = props.Duration ?? TimeSpan.Zero;

                Console.WriteLine(
                    $"{deployment.Data.Name,-40} " +
                    $"{GetStatusDisplay(props.ProvisioningState),-15} " +
                    $"{props.Timestamp?.ToString("yyyy-MM-dd HH:mm:ss") ?? "N/A",-25} " +
                    $"{duration.TotalSeconds:F2}s"
                );

                if (count >= 20)
                {
                    Console.Write("\nShowing first 20 deployments. Continue? (yes/no): ");
                    if (Console.ReadLine()?.ToLower() != "yes") break;
                    count = 0;
                }
            }

            Console.WriteLine($"\nTotal deployments listed: {count}");
        }

        // 4. Get Deployment Details
        static async Task GetDeploymentDetailsAsync()
        {
            Console.Write("\nEnter deployment name: ");
            string deploymentName = Console.ReadLine();

            var deploymentResponse = await _resourceGroup.GetArmDeployments()
                .GetAsync(deploymentName);

            var deployment = deploymentResponse.Value;
            var props = deployment.Data.Properties;

            Console.WriteLine("\n=== Deployment Details ===");
            Console.WriteLine($"Name: {deployment.Data.Name}");
            Console.WriteLine($"Status: {GetStatusDisplay(props.ProvisioningState)}");
            Console.WriteLine($"Timestamp: {props.Timestamp}");
            Console.WriteLine($"Duration: {props.Duration?.TotalSeconds:F2}s");
            Console.WriteLine($"Correlation ID: {props.CorrelationId}");
            Console.WriteLine($"Mode: {props.Mode}");

            if (props.Error != null)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("\nError Information:");
                Console.WriteLine($"Code: {props.Error.Code}");
                Console.WriteLine($"Message: {props.Error.Message}");
                Console.ResetColor();
            }

            if (props.Outputs != null)
            {
                Console.WriteLine("\nOutputs:");
                var outputsJson = props.Outputs.ToString();
                var outputs = JObject.Parse(outputsJson);
                foreach (var output in outputs)
                {
                    Console.WriteLine($"  {output.Key}: {output.Value["value"]}");
                }
            }

            Console.WriteLine($"\nProviders:");
            if (props.Providers != null)
            {
                foreach (var provider in props.Providers)
                {
                    Console.WriteLine($"  {provider.Namespace}");
                }
            }
        }

        // 5. View Deployment Operations
        static async Task ViewDeploymentOperationsAsync()
        {
            Console.Write("\nEnter deployment name: ");
            string deploymentName = Console.ReadLine();

            var deploymentResponse = await _resourceGroup.GetArmDeployments()
                .GetAsync(deploymentName);

            var deployment = deploymentResponse.Value;

            Console.WriteLine("\n=== Deployment Operations ===");

            var operations = deployment.GetArmDeploymentOperations().GetAllAsync();

            Console.WriteLine($"\n{"Operation",-50} {"Status",-15} {"Duration"}");
            Console.WriteLine(new string('-', 80));

            await foreach (var operation in operations)
            {
                var props = operation.Data.Properties;
                var resourceName = props.TargetResource?.ResourceName ?? "N/A";
                var resourceType = props.TargetResource?.ResourceType ?? "Unknown";
                var duration = (props.Timestamp?.Subtract(props.Timestamp ?? DateTime.UtcNow).Duration()) ?? TimeSpan.Zero;

                Console.WriteLine(
                    $"{$"{resourceType}/{resourceName}",-50} " +
                    $"{props.ProvisioningState,-15} " +
                    $"{duration.TotalSeconds:F2}s"
                );

                if (props.StatusMessage != null && props.ProvisioningState != "Succeeded")
                {
                    Console.ForegroundColor = ConsoleColor.Yellow;
                    Console.WriteLine($"    Message: {props.StatusMessage}");
                    Console.ResetColor();
                }
            }
        }
