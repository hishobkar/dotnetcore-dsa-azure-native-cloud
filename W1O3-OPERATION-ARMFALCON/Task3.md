# Task 3: Deploy Resources Using ARM Templates

## Overview
This task focuses on creating and deploying Azure Resource Manager (ARM) templates to provision Azure resources. You'll learn to create ARM templates for deploying storage accounts and virtual machines, and how to deploy them using Azure Portal, Azure CLI, and .NET Core applications.

## Prerequisites
- Active Azure subscription
- Completed Task 1: Introduction to Azure Resource Manager (ARM)
- Completed Task 2: Create and Manage a Resource Group
- Azure CLI installed (https://docs.microsoft.com/cli/azure/install-azure-cli)
- Visual Studio Code with Azure Resource Manager Tools extension (recommended)
- .NET Core SDK 6.0 or later
- Basic understanding of JSON syntax
- Text editor for creating JSON templates

## Estimated Time
**45 minutes**

## Learning Objectives
By the end of this task, you should be able to:
1. Understand ARM template structure and syntax
2. Create ARM templates for storage accounts
3. Create ARM templates for virtual machines
4. Deploy templates using Azure Portal
5. Deploy templates using Azure CLI
6. Deploy templates using .NET Core SDK
7. Validate and troubleshoot ARM template deployments
8. Use parameters and variables in ARM templates
9. Understand deployment modes (incremental vs complete)

## Part 1: ARM Template Fundamentals

### What is an ARM Template?

An ARM (Azure Resource Manager) template is a JSON file that defines the infrastructure and configuration for your Azure solution. It implements **Infrastructure as Code (IaC)**, allowing you to:
- Deploy resources consistently and repeatedly
- Define dependencies between resources
- Version control your infrastructure
- Automate deployments
- Ensure compliance and governance

### ARM Template Structure
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "",
  "parameters": {},
  "variables": {},
  "functions": [],
  "resources": [],
  "outputs": {}
}
```

### Template Sections Explained

| Section | Required | Description |
|---------|----------|-------------|
| `$schema` | Yes | Location of JSON schema file that describes template version |
| `contentVersion` | Yes | Version of the template (e.g., 1.0.0.0) |
| `apiProfile` | No | API version for all resources (rare usage) |
| `parameters` | No | Input values provided during deployment |
| `variables` | No | Values constructed from parameters/expressions |
| `functions` | No | User-defined functions for the template |
| `resources` | Yes | Resource types to deploy or update |
| `outputs` | No | Values returned after deployment |

### Deployment Modes

#### Incremental Mode (Default)
- Adds or updates resources
- Leaves unchanged resources as-is
- Does not delete existing resources
- **Recommended for most scenarios**

#### Complete Mode
- Deploys resources in template
- **Deletes resources not in template**
- Use with extreme caution
- Good for ensuring exact state

## Part 2: Creating an ARM Template for Storage Account

### Storage Account Template - Basic Version

Create a file named `storage-account-basic.json`:
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2023-01-01",
      "name": "mystorageaccount12345",
      "location": "eastus",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2",
      "properties": {
        "supportsHttpsTrafficOnly": true,
        "minimumTlsVersion": "TLS1_2"
      }
    }
  ]
}
```

### Storage Account Template - With Parameters

Create a file named `storage-account.json`:
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24,
      "metadata": {
        "description": "Name of the storage account. Must be globally unique."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the storage account."
      }
    },
    "sku": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Standard_ZRS",
        "Premium_LRS",
        "Premium_ZRS"
      ],
      "metadata": {
        "description": "Storage account SKU"
      }
    },
    "kind": {
      "type": "string",
      "defaultValue": "StorageV2",
      "allowedValues": [
        "Storage",
        "StorageV2",
        "BlobStorage",
        "FileStorage",
        "BlockBlobStorage"
      ],
      "metadata": {
        "description": "Storage account kind"
      }
    },
    "tags": {
      "type": "object",
      "defaultValue": {
        "Environment": "Development",
        "Project": "ARM-Template-Demo"
      },
      "metadata": {
        "description": "Tags to apply to the storage account"
      }
    }
  },
  "variables": {
    "uniqueStorageName": "[toLower(concat(parameters('storageAccountName'), uniqueString(resourceGroup().id)))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2023-01-01",
      "name": "[variables('uniqueStorageName')]",
      "location": "[parameters('location')]",
      "tags": "[parameters('tags')]",
      "sku": {
        "name": "[parameters('sku')]"
      },
      "kind": "[parameters('kind')]",
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
        "accessTier": "Hot",
        "allowBlobPublicAccess": false,
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('uniqueStorageName')]"
    },
    "storageAccountId": {
      "type": "string",
      "value": "[resourceId('Microsoft.Storage/storageAccounts', variables('uniqueStorageName'))]"
    },
    "primaryEndpoints": {
      "type": "object",
      "value": "[reference(variables('uniqueStorageName')).primaryEndpoints]"
    }
  }
}
```

### Storage Account Parameters File

Create a file named `storage-account.parameters.json`:
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "value": "myappstorage"
    },
    "sku": {
      "value": "Standard_LRS"
    },
    "kind": {
      "value": "StorageV2"
    },
    "tags": {
      "value": {
        "Environment": "Development",
        "Project": "DotNetCoreApp",
        "CostCenter": "IT-Development"
      }
    }
  }
}
```

## Part 3: Creating an ARM Template for Virtual Machine

### Virtual Machine Template - Complete

Create a file named `virtual-machine.json`:
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Name of the virtual machine"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username for the VM"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password for the VM"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_B2s",
      "allowedValues": [
        "Standard_B1s",
        "Standard_B2s",
        "Standard_B2ms",
        "Standard_D2s_v3",
        "Standard_D4s_v3"
      ],
      "metadata": {
        "description": "Size of the virtual machine"
      }
    },
    "osType": {
      "type": "string",
      "defaultValue": "Windows",
      "allowedValues": [
        "Windows",
        "Linux"
      ],
      "metadata": {
        "description": "Operating system type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources"
      }
    }
  },
  "variables": {
    "vnetName": "[concat(parameters('vmName'), '-vnet')]",
    "subnetName": "default",
    "nicName": "[concat(parameters('vmName'), '-nic')]",
    "publicIPName": "[concat(parameters('vmName'), '-pip')]",
    "nsgName": "[concat(parameters('vmName'), '-nsg')]",
    "addressPrefix": "10.0.0.0/16",
    "subnetPrefix": "10.0.0.0/24",
    "windowsImage": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "2022-Datacenter",
      "version": "latest"
    },
    "linuxImage": {
      "publisher": "Canonical",
      "offer": "0001-com-ubuntu-server-jammy",
      "sku": "22_04-lts-gen2",
      "version": "latest"
    },
    "imageReference": "[if(equals(parameters('osType'), 'Windows'), variables('windowsImage'), variables('linuxImage'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "apiVersion": "2023-04-01",
      "name": "[variables('nsgName')]",
      "location": "[parameters('location')]",
      "properties": {
        "securityRules": [
          {
            "name": "RDP",
            "properties": {
              "priority": 1000,
              "protocol": "Tcp",
              "access": "Allow",
              "direction": "Inbound",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*",
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389"
            }
          },
          {
            "name": "SSH",
            "properties": {
              "priority": 1001,
              "protocol": "Tcp",
              "access": "Allow",
              "direction": "Inbound",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*",
              "destinationAddressPrefix": "*",
              "destinationPortRange": "22"
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2023-04-01",
      "name": "[variables('publicIPName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "publicIPAllocationMethod": "Static"
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2023-04-01",
      "name": "[variables('vnetName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkSecurityGroups', variables('nsgName'))]"
      ],
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]",
              "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('nsgName'))]"
              }
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2023-04-01",
      "name": "[variables('nicName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks', variables('vnetName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPName'))]"
              },
              "subnet": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('vnetName'), variables('subnetName'))]"
              }
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2023-03-01",
      "name": "[parameters('vmName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkInterfaces', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computerName": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": "[variables('imageReference')]",
          "osDisk": {
            "createOption": "FromImage",
            "managedDisk": {
              "storageAccountType": "Premium_LRS"
            }
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('nicName'))]"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "vmName": {
      "type": "string",
      "value": "[parameters('vmName')]"
    },
    "publicIP": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPName'))).ipAddress]"
    },
    "adminUsername": {
      "type": "string",
      "value": "[parameters('adminUsername')]"
    }
  }
}
```

### Virtual Machine Parameters File

Create a file named `virtual-machine.parameters.json`:
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "value": "myDevVM"
    },
    "adminUsername": {
      "value": "azureuser"
    },
    "adminPassword": {
      "value": "P@ssw0rd123!@#"
    },
    "vmSize": {
      "value": "Standard_B2s"
    },
    "osType": {
      "value": "Windows"
    }
  }
}
```

## Part 4: Deploying ARM Templates Using Azure Portal

### Step-by-Step: Deploy Storage Account Template

#### Step 1: Navigate to Custom Deployment
1. Sign in to Azure Portal (https://portal.azure.com)
2. Search for "Deploy a custom template" in the search bar
3. Click on **Deploy a custom template**

#### Step 2: Load Your Template
1. Click **Build your own template in the editor**
2. Copy and paste the `storage-account.json` content
3. Click **Save**

#### Step 3: Configure Deployment
1. **Subscription**: Select your subscription
2. **Resource group**: 
   - Create new: `rg-arm-demo-storage`
   - Or select existing
3. **Region**: Select your preferred region
4. **Storage Account Name**: Enter unique name (e.g., `myappstorage`)
5. **Sku**: Select `Standard_LRS`
6. **Kind**: Select `StorageV2`
7. Review other parameters

#### Step 4: Review and Deploy
1. Click **Review + create**
2. Review the template and parameters
3. Click **Create**
4. Monitor deployment progress

#### Step 5: Verify Deployment
1. Click **Go to resource group**
2. Verify the storage account is created
3. Check outputs in deployment details
4. Verify tags are applied

### Deploying Virtual Machine Template

Follow similar steps for VM deployment:
1. Use custom deployment in portal
2. Load `virtual-machine.json` template
3. Provide required parameters (ensure strong password)
4. Deploy to new resource group: `rg-arm-demo-vm`
5. Wait for deployment (takes 5-10 minutes)

## Part 5: Deploying ARM Templates Using Azure CLI

### Prerequisites
```bash
# Install Azure CLI (if not already installed)
# Windows: Download from https://aka.ms/installazurecliwindows
# macOS: brew install azure-cli
# Linux: curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Verify installation
az --version

# Login to Azure
az login

# Set default subscription (if you have multiple)
az account set --subscription "Your Subscription Name"
```

### Deploy Storage Account Template
```bash
# Create resource group
az group create \
  --name rg-arm-cli-storage \
  --location eastus

# Validate template
az deployment group validate \
  --resource-group rg-arm-cli-storage \
  --template-file storage-account.json \
  --parameters storage-account.parameters.json

# Deploy template
az deployment group create \
  --name storage-deployment \
  --resource-group rg-arm-cli-storage \
  --template-file storage-account.json \
  --parameters storage-account.parameters.json

# Check deployment status
az deployment group show \
  --name storage-deployment \
  --resource-group rg-arm-cli-storage

# Get deployment outputs
az deployment group show \
  --name storage-deployment \
  --resource-group rg-arm-cli-storage \
  --query properties.outputs
```

### Deploy with Inline Parameters
```bash
az deployment group create \
  --name storage-inline \
  --resource-group rg-arm-cli-storage \
  --template-file storage-account.json \
  --parameters \
    storageAccountName=myclistorage \
    sku=Standard_LRS \
    kind=StorageV2
```

### Deploy Virtual Machine Template
```bash
# Create resource group
az group create \
  --name rg-arm-cli-vm \
  --location eastus

# Deploy VM template
az deployment group create \
  --name vm-deployment \
  --resource-group rg-arm-cli-vm \
  --template-file virtual-machine.json \
  --parameters \
    vmName=myCliVM \
    adminUsername=azureuser \
    adminPassword='YourSecurePassword123!@#' \
    vmSize=Standard_B2s \
    osType=Windows

# Monitor deployment
az deployment group show \
  --name vm-deployment \
  --resource-group rg-arm-cli-vm \
  --query properties.provisioningState

# Get VM public IP
az deployment group show \
  --name vm-deployment \
  --resource-group rg-arm-cli-vm \
  --query properties.outputs.publicIP.value
```

### What-If Deployment (Preview Changes)
```bash
# See what would change without deploying
az deployment group what-if \
  --resource-group rg-arm-cli-storage \
  --template-file storage-account.json \
  --parameters storage-account.parameters.json
```

## Part 6: Deploying ARM Templates Using .NET Core

### Project Setup
```bash
# Create new console application
dotnet new console -n ArmTemplateDeployer
cd ArmTemplateDeployer

# Add required packages
dotnet add package Azure.Identity
dotnet add package Azure.ResourceManager
dotnet add package Azure.ResourceManager.Resources
dotnet add package Microsoft.Extensions.Configuration
dotnet add package Microsoft.Extensions.Configuration.Json
dotnet add package Newtonsoft.Json
```

### Project Structure
```
ArmTemplateDeployer/
├── ArmTemplateDeployer.csproj
├── Program.cs
├── appsettings.json
├── Templates/
│   ├── storage-account.json
│   ├── storage-account.parameters.json
│   ├── virtual-machine.json
│   └── virtual-machine.parameters.json
└── Helpers/
    └── TemplateHelper.cs
```

### Configuration File (appsettings.json)
```json
{
  "Azure": {
    "SubscriptionId": "your-subscription-id",
    "TenantId": "your-tenant-id",
    "ClientId": "your-client-id",
    "ClientSecret": "your-client-secret"
  },
  "Deployment": {
    "ResourceGroupName": "rg-arm-dotnet-demo",
    "Location": "eastus",
    "TemplatePath": "Templates/storage-account.json",
    "ParametersPath": "Templates/storage-account.parameters.json"
  }
}
```

### Helper Class (TemplateHelper.cs)
```csharp
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Collections.Generic;

namespace ArmTemplateDeployer.Helpers
{
    public static class TemplateHelper
    {
        public static object LoadTemplate(string templatePath)
        {
            if (!File.Exists(templatePath))
            {
                throw new FileNotFoundException($"Template file not found: {templatePath}");
            }

            string templateContent = File.ReadAllText(templatePath);
            return JsonConvert.DeserializeObject<object>(templateContent);
        }

        public static Dictionary<string, object> LoadParameters(string parametersPath)
        {
            if (!File.Exists(parametersPath))
            {
                throw new FileNotFoundException($"Parameters file not found: {parametersPath}");
            }

            string parametersContent = File.ReadAllText(parametersPath);
            var parametersObject = JObject.Parse(parametersContent);
            
            var parameters = new Dictionary<string, object>();
            
            if (parametersObject["parameters"] != null)
            {
                foreach (var param in parametersObject["parameters"])
                {
                    var property = (JProperty)param;
                    var paramValue = property.Value["value"];
                    
                    parameters[property.Name] = new
                    {
                        value = paramValue
                    };
                }
            }

            return parameters;
        }

        public static void ValidateTemplate(object template)
        {
            var json = JsonConvert.SerializeObject(template);
            var templateObject = JObject.Parse(json);

            if (templateObject["$schema"] == null)
            {
                throw new InvalidOperationException("Template missing $schema property");
            }

            if (templateObject["resources"] == null)
            {
                throw new InvalidOperationException("Template missing resources section");
            }

            Console.WriteLine("✓ Template validation passed");
        }

        public static void DisplayTemplate(object template)
        {
            string json = JsonConvert.SerializeObject(template, Formatting.Indented);
            Console.WriteLine("\n=== Template Content ===");
            Console.WriteLine(json);
        }

        public static void DisplayParameters(Dictionary<string, object> parameters)
        {
            Console.WriteLine("\n=== Deployment Parameters ===");
            foreach (var param in parameters)
            {
                var paramObj = JObject.FromObject(param.Value);
                Console.WriteLine($"{param.Key}: {paramObj["value"]}");
            }
        }
    }
}
```

### Main Program (Program.cs)
```csharp
using Azure;
using Azure.Core;
using Azure.Identity;
using Azure.ResourceManager;
using Azure.ResourceManager.Resources;
using Azure.ResourceManager.Resources.Models;
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text.Json;
using System.Threading.Tasks;
using ArmTemplateDeployer.Helpers;

namespace ArmTemplateDeployer
{
    class Program
    {
        private static IConfiguration _configuration;
        private static ArmClient _armClient;
        private static SubscriptionResource _subscription;

        static async Task Main(string[] args)
        {
            try
            {
                Console.WriteLine("=== ARM Template Deployment Tool ===\n");

                // Load configuration
                LoadConfiguration();

                // Initialize Azure client
                await InitializeAzureClient();

                // Main menu
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
                .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
                .Build();

            Console.WriteLine("✓ Configuration loaded");
        }

        static async Task InitializeAzureClient()
        {
            // Use DefaultAzureCredential for authentication
            var credential = new DefaultAzureCredential();

            _armClient = new ArmClient(credential);

            string subscriptionId = _configuration["Azure:SubscriptionId"];
            _subscription = _armClient.GetSubscriptionResource(
                new ResourceIdentifier($"/subscriptions/{subscriptionId}")
            );

            Console.WriteLine($"✓ Connected to subscription: {_subscription.Data.DisplayName}\n");
        }

        static async Task RunMenuAsync()
        {
            while (true)
            {
                Console.WriteLine("\n=== Deployment Options ===");
                Console.WriteLine("1. Deploy Storage Account");
                Console.WriteLine("2. Deploy Virtual Machine");
                Console.WriteLine("3. Validate Template");
                Console.WriteLine("4. List Deployments");
                Console.WriteLine("5. Get Deployment Details");
                Console.WriteLine("6. Delete Deployment");
                Console.WriteLine("7. Exit");
                Console.Write("\nSelect an option: ");

                string choice = Console.ReadLine();

                try
                {
                    switch (choice)
                    {
                        case "1":
                            await DeployStorageAccountAsync();
                            break;
                        case "2":
                            await DeployVirtualMachineAsync();
                            break;
                        case "3":
                            await ValidateTemplateAsync();
                            break;
                        case "4":
                            await ListDeploymentsAsync();
                            break;
                        case "5":
                            await GetDeploymentDetailsAsync();
                            break;
                        case "6":
                            await DeleteDeploymentAsync();
                            break;
                        case "7":
                            Console.WriteLine("Exiting...");
                            return;
                        default:
                            Console.WriteLine("Invalid option. Please try again.");
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
                    Console.ResetColor();
                }
            }
        }

        static async Task DeployStorageAccountAsync()
        {
            Console.WriteLine("\n=== Deploy Storage Account ===");

            // Ensure resource group exists
            string resourceGroupName = _configuration["Deployment:ResourceGroupName"];
            string location = _configuration["Deployment:Location"];
            
            var resourceGroup = await EnsureResourceGroupAsync(resourceGroupName, location);

            // Load template and parameters
            string templatePath = "Templates/storage-account.json";
            string parametersPath = "Templates/storage-account.parameters.json";

            Console.WriteLine($"\nLoading template: {templatePath}");
            var template = TemplateHelper.LoadTemplate(templatePath);
            
            Console.WriteLine($"Loading parameters: {parametersPath}");
            var parameters = TemplateHelper.LoadParameters(parametersPath);

            // Display loaded content
            TemplateHelper.DisplayParameters(parameters);

            // Validate template
            TemplateHelper.ValidateTemplate(template);

            Console.Write("\nProceed with deployment? (yes/no): ");
            if (Console.ReadLine()?.ToLower() != "yes")
            {
                Console.WriteLine("Deployment cancelled.");
                return;
            }

            // Create deployment
            string deploymentName = $"storage-deployment-{DateTime.Now:yyyyMMdd-HHmmss}";
            
            Console.WriteLine($"\nStarting deployment: {deploymentName}");
            Console.WriteLine("This may take a few minutes...");

            var deploymentContent = new ArmDeploymentContent(
                new ArmDeploymentProperties(ArmDeploymentMode.Incremental)
                {
                    Template = BinaryData.FromObjectAsJson(template),
                    Parameters = BinaryData.FromObjectAsJson(parameters)
                }
            );

            var deploymentOperation = await resourceGroup.GetArmDeployments()
                .CreateOrUpdateAsync(
                    WaitUntil.Completed,
                    deploymentName,
                    deploymentContent
                );

            var deployment = deploymentOperation.Value;

            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine($"\n✓ Deployment '{deploymentName}' completed successfully!");
            Console.ResetColor();

            // Display outputs
            DisplayDeploymentOutputs(deployment);
        }
    }
}
```

## 7: Validate and Troubleshoot ARM Template Deployments  

### Objective  
Learn how to verify, test, and debug issues in ARM (Azure Resource Manager) template deployments.

### Steps  
1. Open Azure Portal or use Azure CLI for validation.  
2. Run validation before deployment to catch syntax and configuration errors.  
   ```
   az deployment group validate \
     --resource-group myResourceGroup \
     --template-file azuredeploy.json \
     --parameters azuredeploy.parameters.json
   ```
3. If validation fails, review the error message and fix the reported property or missing parameter.  
4. For troubleshooting failed deployments:  
   - In Azure Portal, go to **Deployments** under the resource group.  
   - Review the **Operation Details** tab for failed resources.  
5. Use logs for deep analysis:  
   ```
   az monitor activity-log list --resource-group myResourceGroup --status Failed
   ```

### Key Takeaways  
- Validation identifies JSON syntax and schema issues.  
- Deployment failures are usually due to misconfigured dependencies or incorrect parameters.

---

## Task 8: Use Parameters and Variables in ARM Templates  

### Objective  
Learn how to apply modular and reusable design with parameters and variables in ARM templates.

### Steps  
1. Define **parameters** in `azuredeploy.json` for dynamic inputs such as resource names or locations.  
   ```
   "parameters": {
     "storageAccountName": {
       "type": "string",
       "defaultValue": "mystorageaccount",
       "metadata": {
         "description": "Name of the storage account."
       }
     }
   }
   ```
2. Define **variables** for reusable computed values.  
   ```
   "variables": {
     "storageAccountType": "Standard_LRS"
   }
   ```
3. Reference parameters and variables in resource definitions:  
   ```
   "type": "Microsoft.Storage/storageAccounts",
   "name": "[parameters('storageAccountName')]",
   "sku": {
     "name": "[variables('storageAccountType')]"
   }
   ```
4. Pass parameters through the CLI or a parameters file:  
   ```
   az deployment group create \
     --resource-group myResourceGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountName=mycustomstorage
   ```

### Key Takeaways  
- Parameters increase flexibility.  
- Variables reduce repetition and simplify complex template logic.

---

## Task 9: Understand Deployment Modes (Incremental vs Complete) 

### Objective  
Understand how ARM template deployment modes affect updates to existing Azure resources.

### Concepts  

| Mode | Description | Behavior |
|------|--------------|-----------|
| Incremental | Adds or updates resources in the resource group. | Existing resources not in the template remain unchanged. |
| Complete | Replaces resource group contents with the template definition. | Resources not defined in the template are **deleted**. |

### Usage Examples  
Deploy in **incremental** mode (default):  
```
az deployment group create \
  --mode Incremental \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

Deploy in **complete** mode:  
```
az deployment group create \
  --mode Complete \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

### Key Takeaways  
- Use Incremental mode for safe updates.  
- Use Complete mode for enforcing a strict desired state.
