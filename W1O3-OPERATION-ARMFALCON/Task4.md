# Task 4: Parameterization and Variables in ARM Templates

## Overview
This task focuses on enhancing ARM templates with parameters and variables to make them flexible, reusable, and maintainable. You'll learn how to use parameters for user inputs, variables for computed values, template functions, and how to implement these concepts in .NET Core applications.

## Prerequisites
- Active Azure subscription
- Completed Task 1: Introduction to Azure Resource Manager (ARM)
- Completed Task 2: Create and Manage a Resource Group
- Completed Task 3: Deploy Resources Using ARM Templates
- Visual Studio Code with Azure Resource Manager Tools extension
- .NET Core SDK 6.0 or later
- Azure CLI installed
- Basic understanding of JSON and ARM template structure

## Estimated Time
**45 minutes**

## Learning Objectives
By the end of this task, you should be able to:
1. Understand the difference between parameters and variables
2. Create and use parameters with various data types
3. Implement parameter constraints and validation
4. Use variables for computed values and expressions
5. Utilize ARM template functions effectively
6. Implement parameter files for different environments
7. Use variables to simplify complex expressions
8. Handle sensitive data with secure parameters
9. Implement parameterization in .NET Core applications
10. Follow best practices for template parameterization

## Part 1: Understanding Parameters and Variables

### Parameters vs Variables

| Aspect | Parameters | Variables |
|--------|-----------|-----------|
| **Purpose** | Accept input values during deployment | Store computed values and expressions |
| **Source** | User-provided or parameter file | Defined within the template |
| **When Set** | At deployment time | At template compilation time |
| **Mutability** | Cannot change during deployment | Cannot change during deployment |
| **Use Case** | Environment-specific values | Simplify complex expressions |
| **Examples** | VM size, location, admin username | Resource names, connection strings |

### When to Use Parameters
- Values that differ between environments (dev, test, prod)
- User-specific configurations
- Sensitive information (passwords, keys)
- Values that change between deployments
- Customizable settings

### When to Use Variables
- Computed values based on parameters
- Complex expressions used multiple times
- Resource naming conventions
- Default configurations
- Intermediate calculations

## Part 2: Working with Parameters

### Parameter Data Types

ARM templates support the following parameter types:

| Type | Description | Example |
|------|-------------|---------|
| `string` | Text value | "Standard_LRS" |
| `int` | Integer number | 100 |
| `bool` | Boolean value | true or false |
| `object` | JSON object | {"key": "value"} |
| `array` | JSON array | ["value1", "value2"] |
| `securestring` | Encrypted string | Passwords, keys |
| `secureObject` | Encrypted object | Complex secure data |

### Basic Parameter Syntax
```json
{
  "parameters": {
    "parameterName": {
      "type": "string",
      "defaultValue": "default-value",
      "allowedValues": [],
      "minValue": 0,
      "maxValue": 100,
      "minLength": 1,
      "maxLength": 64,
      "metadata": {
        "description": "Description of the parameter"
      }
    }
  }
}
```

### String Parameters
```json
{
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24,
      "metadata": {
        "description": "Storage account name (3-24 chars, lowercase, alphanumeric)"
      }
    },
    "environment": {
      "type": "string",
      "defaultValue": "dev",
      "allowedValues": [
        "dev",
        "test",
        "staging",
        "prod"
      ],
      "metadata": {
        "description": "Environment name"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Azure region for resources"
      }
    }
  }
}
```

### Integer Parameters
```json
{
  "parameters": {
    "instanceCount": {
      "type": "int",
      "defaultValue": 2,
      "minValue": 1,
      "maxValue": 10,
      "metadata": {
        "description": "Number of VM instances (1-10)"
      }
    },
    "diskSizeGB": {
      "type": "int",
      "defaultValue": 128,
      "allowedValues": [
        32,
        64,
        128,
        256,
        512,
        1024
      ],
      "metadata": {
        "description": "OS disk size in GB"
      }
    }
  }
}
```

### Boolean Parameters
```json
{
  "parameters": {
    "enableBackup": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Enable automated backups"
      }
    },
    "enableMonitoring": {
      "type": "bool",
      "defaultValue": false,
      "metadata": {
        "description": "Enable Azure Monitor integration"
      }
    }
  }
}
```

### Object Parameters
```json
{
  "parameters": {
    "tags": {
      "type": "object",
      "defaultValue": {
        "Environment": "Development",
        "CostCenter": "IT",
        "Owner": "DevOps Team"
      },
      "metadata": {
        "description": "Tags to apply to all resources"
      }
    },
    "networkConfig": {
      "type": "object",
      "defaultValue": {
        "addressPrefix": "10.0.0.0/16",
        "subnets": [
          {
            "name": "web",
            "addressPrefix": "10.0.1.0/24"
          },
          {
            "name": "app",
            "addressPrefix": "10.0.2.0/24"
          },
          {
            "name": "data",
            "addressPrefix": "10.0.3.0/24"
          }
        ]
      },
      "metadata": {
        "description": "Virtual network configuration"
      }
    }
  }
}
```

### Array Parameters
```json
{
  "parameters": {
    "allowedIPs": {
      "type": "array",
      "defaultValue": [
        "203.0.113.1",
        "203.0.113.2",
        "203.0.113.3"
      ],
      "metadata": {
        "description": "List of allowed IP addresses"
      }
    },
    "vmSizes": {
      "type": "array",
      "defaultValue": [
        "Standard_B2s",
        "Standard_D2s_v3",
        "Standard_D4s_v3"
      ],
      "metadata": {
        "description": "Allowed VM sizes"
      }
    }
  }
}
```

### Secure Parameters
```json
{
  "parameters": {
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Administrator password for VM"
      }
    },
    "sqlConnectionString": {
      "type": "securestring",
      "metadata": {
        "description": "SQL Server connection string"
      }
    },
    "secrets": {
      "type": "secureObject",
      "metadata": {
        "description": "Collection of secret values"
      }
    }
  }
}
```

## Part 3: Working with Variables

### Basic Variable Syntax
```json
{
  "variables": {
    "variableName": "value",
    "computedValue": "[expression]",
    "objectVariable": {
      "property1": "value1",
      "property2": "value2"
    }
  }
}
```

### String Variables
```json
{
  "parameters": {
    "projectName": {
      "type": "string"
    },
    "environment": {
      "type": "string"
    }
  },
  "variables": {
    "resourcePrefix": "[concat(parameters('projectName'), '-', parameters('environment'))]",
    "storageAccountName": "[toLower(concat(variables('resourcePrefix'), uniqueString(resourceGroup().id)))]",
    "location": "[resourceGroup().location]",
    "vnetName": "[concat(variables('resourcePrefix'), '-vnet')]",
    "subnetName": "[concat(variables('resourcePrefix'), '-subnet')]",
    "nsgName": "[concat(variables('resourcePrefix'), '-nsg')]"
  }
}
```

### Object Variables
```json
{
  "variables": {
    "networkConfig": {
      "vnetName": "myVNet",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "web": {
          "name": "web-subnet",
          "addressPrefix": "10.0.1.0/24"
        },
        "app": {
          "name": "app-subnet",
          "addressPrefix": "10.0.2.0/24"
        }
      }
    },
    "vmConfig": {
      "windows": {
        "publisher": "MicrosoftWindowsServer",
        "offer": "WindowsServer",
        "sku": "2022-Datacenter",
        "version": "latest"
      },
      "linux": {
        "publisher": "Canonical",
        "offer": "0001-com-ubuntu-server-jammy",
        "sku": "22_04-lts-gen2",
        "version": "latest"
      }
    }
  }
}
```

### Array Variables
```json
{
  "variables": {
    "allowedLocations": [
      "eastus",
      "westus",
      "centralus"
    ],
    "subnets": [
      {
        "name": "GatewaySubnet",
        "addressPrefix": "10.0.0.0/27"
      },
      {
        "name": "web-subnet",
        "addressPrefix": "10.0.1.0/24"
      },
      {
        "name": "app-subnet",
        "addressPrefix": "10.0.2.0/24"
      }
    ],
    "nsgRules": [
      {
        "name": "AllowHTTP",
        "priority": 100,
        "direction": "Inbound",
        "access": "Allow",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      },
      {
        "name": "AllowHTTPS",
        "priority": 110,
        "direction": "Inbound",
        "access": "Allow",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "443",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      }
    ]
  }
}
```

## Part 4: ARM Template Functions

### String Functions
```json
{
  "variables": {
    // Concatenation
    "fullName": "[concat(parameters('firstName'), ' ', parameters('lastName'))]",
    
    // Convert to lowercase
    "lowerCaseName": "[toLower(parameters('name'))]",
    
    // Convert to uppercase
    "upperCaseName": "[toUpper(parameters('name'))]",
    
    // Replace text
    "sanitizedName": "[replace(parameters('name'), ' ', '-')]",
    
    // Substring
    "shortName": "[substring(parameters('name'), 0, 8)]",
    
    // String length
    "nameLength": "[length(parameters('name'))]",
    
    // Split string
    "nameParts": "[split(parameters('name'), '-')]",
    
    // Trim whitespace
    "trimmedName": "[trim(parameters('name'))]",
    
    // URI component encoding
    "encodedName": "[uriComponent(parameters('name'))]",
    
    // Unique string (deterministic hash)
    "uniqueName": "[uniqueString(resourceGroup().id, parameters('name'))]"
  }
}
```

### Numeric Functions
```json
{
  "variables": {
    // Addition
    "totalCount": "[add(parameters('webServers'), parameters('appServers'))]",
    
    // Subtraction
    "difference": "[sub(parameters('maxSize'), parameters('minSize'))]",
    
    // Multiplication
    "totalCost": "[mul(parameters('instanceCount'), parameters('costPerInstance'))]",
    
    // Division
    "averageCost": "[div(parameters('totalCost'), parameters('instanceCount'))]",
    
    // Modulo
    "remainder": "[mod(parameters('value'), 10)]",
    
    // Minimum value
    "minValue": "[min(parameters('value1'), parameters('value2'))]",
    
    // Maximum value
    "maxValue": "[max(parameters('value1'), parameters('value2'))]",
    
    // Integer conversion
    "intValue": "[int(parameters('stringNumber'))]",
    
    // Float conversion
    "floatValue": "[float(parameters('stringNumber'))]"
  }
}
```

### Logical Functions
```json
{
  "variables": {
    // Conditional (if-then-else)
    "vmSize": "[if(equals(parameters('environment'), 'prod'), 'Standard_D4s_v3', 'Standard_B2s')]",
    
    // And operator
    "isProductionHighAvailability": "[and(equals(parameters('environment'), 'prod'), parameters('enableHA'))]",
    
    // Or operator
    "needsBackup": "[or(equals(parameters('environment'), 'prod'), parameters('forceBackup'))]",
    
    // Not operator
    "isDevelopment": "[not(equals(parameters('environment'), 'prod'))]",
    
    // Equals comparison
    "isProduction": "[equals(parameters('environment'), 'prod')]",
    
    // Greater than
    "needsScaling": "[greater(parameters('instanceCount'), 5)]",
    
    // Less than
    "isSmallDeployment": "[less(parameters('instanceCount'), 3)]"
  }
}
```

### Array and Object Functions
```json
{
  "variables": {
    // Array length
    "subnetCount": "[length(parameters('subnets'))]",
    
    // First element
    "firstSubnet": "[first(parameters('subnets'))]",
    
    // Last element
    "lastSubnet": "[last(parameters('subnets'))]",
    
    // Check if array contains value
    "hasWebSubnet": "[contains(parameters('subnetNames'), 'web')]",
    
    // Get array element by index
    "secondSubnet": "[parameters('subnets')[1]]",
    
    // Join array to string
    "subnetList": "[join(parameters('subnetNames'), ', ')]",
    
    // Create array range
    "numbers": "[range(1, 5)]",
    
    // Union of objects
    "mergedTags": "[union(parameters('defaultTags'), parameters('customTags'))]",
    
    // Get object keys
    "tagNames": "[keys(parameters('tags'))]",
    
    // Check if object has key
    "hasEnvironmentTag": "[contains(parameters('tags'), 'Environment')]"
  }
}
```

### Resource Functions
```json
{
  "variables": {
    // Get resource group information
    "rgLocation": "[resourceGroup().location]",
    "rgName": "[resourceGroup().name]",
    "rgId": "[resourceGroup().id]",
    
    // Get subscription information
    "subscriptionId": "[subscription().subscriptionId]",
    "tenantId": "[subscription().tenantId]",
    
    // Build resource ID
    "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    
    // Reference existing resource
    "subnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('vnetName'), variables('subnetName'))]",
    
    // Get deployment information
    "deploymentName": "[deployment().name]"
  }
}
```

## Part 5: Complete Parameterized Template Example

### Multi-Tier Web Application Template

Create a file named `web-app-complete.json`:
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 10,
      "metadata": {
        "description": "Project name (3-10 characters, alphanumeric)"
      }
    },
    "environment": {
      "type": "string",
      "defaultValue": "dev",
      "allowedValues": [
        "dev",
        "test",
        "staging",
        "prod"
      ],
      "metadata": {
        "description": "Environment name"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Azure region for resources"
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage account replication type"
      }
    },
    "appServicePlanSku": {
      "type": "object",
      "defaultValue": {
        "name": "F1",
        "tier": "Free",
        "capacity": 1
      },
      "metadata": {
        "description": "App Service Plan SKU configuration"
      }
    },
    "enableApplicationInsights": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Enable Application Insights monitoring"
      }
    },
    "sqlAdminUsername": {
      "type": "string",
      "metadata": {
        "description": "SQL Server admin username"
      }
    },
    "sqlAdminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "SQL Server admin password"
      }
    },
    "databaseSizeGB": {
      "type": "int",
      "defaultValue": 2,
      "allowedValues": [
        2,
        5,
        10,
        20,
        50
      ],
      "metadata": {
        "description": "Database size in GB"
      }
    },
    "tags": {
      "type": "object",
      "defaultValue": {},
      "metadata": {
        "description": "Additional tags for resources"
      }
    }
  },
  "variables": {
    // Naming convention variables
    "resourcePrefix": "[toLower(concat(parameters('projectName'), '-', parameters('environment')))]",
    "uniqueSuffix": "[substring(uniqueString(resourceGroup().id), 0, 6)]",
    
    // Storage Account
    "storageAccountName": "[toLower(concat(replace(variables('resourcePrefix'), '-', ''), variables('uniqueSuffix')))]",
    
    // App Service
    "appServicePlanName": "[concat(variables('resourcePrefix'), '-asp')]",
    "webAppName": "[concat(variables('resourcePrefix'), '-web-', variables('uniqueSuffix'))]",
    
    // SQL Server and Database
    "sqlServerName": "[concat(variables('resourcePrefix'), '-sql-', variables('uniqueSuffix'))]",
    "databaseName": "[concat(variables('resourcePrefix'), '-db')]",
    
    // Application Insights
    "appInsightsName": "[concat(variables('resourcePrefix'), '-ai')]",
    
    // Tag merging
    "defaultTags": {
      "Project": "[parameters('projectName')]",
      "Environment": "[parameters('environment')]",
      "ManagedBy": "ARM-Template",
      "CreatedDate": "[utcNow('yyyy-MM-dd')]"
    },
    "allTags": "[union(variables('defaultTags'), parameters('tags'))]",
    
    // Environment-specific configurations
    "environmentConfig": {
      "dev": {
        "databaseSku": "Basic",
        "databaseMaxSize": 2147483648,
        "appServiceSku": {
          "name": "F1",
          "tier": "Free",
          "capacity": 1
        }
      },
      "test": {
        "databaseSku": "S0",
        "databaseMaxSize": 268435456000,
        "appServiceSku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        }
      },
      "staging": {
        "databaseSku": "S1",
        "databaseMaxSize": 268435456000,
        "appServiceSku": {
          "name": "S2",
          "tier": "Standard",
          "capacity": 2
        }
      },
      "prod": {
        "databaseSku": "S3",
        "databaseMaxSize": 268435456000,
        "appServiceSku": {
          "name": "P1v2",
          "tier": "PremiumV2",
          "capacity": 3
        }
      }
    },
    "currentEnvironmentConfig": "[variables('environmentConfig')[parameters('environment')]]",
    
    // Connection string template
    "sqlConnectionStringTemplate": "[concat('Server=tcp:', variables('sqlServerName'), '.database.windows.net,1433;Initial Catalog=', variables('databaseName'), ';Persist Security Info=False;User ID=', parameters('sqlAdminUsername'), ';Password=', parameters('sqlAdminPassword'), ';MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2023-01-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "tags": "[variables('allTags')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
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
        }
      }
    },
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2022-03-01",
      "name": "[variables('appServicePlanName')]",
      "location": "[parameters('location')]",
      "tags": "[variables('allTags')]",
      "sku": "[if(empty(parameters('appServicePlanSku')), variables('currentEnvironmentConfig').appServiceSku, parameters('appServicePlanSku'))]",
      "properties": {
        "reserved": false
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2022-03-01",
      "name": "[variables('webAppName')]",
      "location": "[parameters('location')]",
      "tags": "[variables('allTags')]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('appServicePlanName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('appServicePlanName'))]",
        "httpsOnly": true,
        "siteConfig": {
          "netFrameworkVersion": "v6.0",
          "appSettings": [
            {
              "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
              "value": "[if(parameters('enableApplicationInsights'), reference(resourceId('Microsoft.Insights/components', variables('appInsightsName'))).InstrumentationKey, '')]"
            },
            {
              "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
              "value": "~3"
            },
            {
              "name": "Environment",
              "value": "[parameters('environment')]"
            },
            {
              "name": "StorageConnectionString",
              "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2023-01-01').keys[0].value, ';EndpointSuffix=core.windows.net')]"
            }
          ],
          "connectionStrings": [
            {
              "name": "DefaultConnection",
              "connectionString": "[variables('sqlConnectionStringTemplate')]",
              "type": "SQLAzure"
            }
          ]
        }
      }
    },
    {
      "condition": "[parameters('enableApplicationInsights')]",
      "type": "Microsoft.Insights/components",
      "apiVersion": "2020-02-02",
      "name": "[variables('appInsightsName')]",
      "location": "[parameters('location')]",
      "tags": "[variables('allTags')]",
      "kind": "web",
      "properties": {
        "Application_Type": "web",
        "RetentionInDays": 90,
        "publicNetworkAccessForIngestion": "Enabled",
        "publicNetworkAccessForQuery": "Enabled"
      }
    },
    {
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2022-05-01-preview",
      "name": "[variables('sqlServerName')]",
      "location": "[parameters('location')]",
      "tags": "[variables('allTags')]",
      "properties": {
        "administratorLogin": "[parameters('sqlAdminUsername')]",
        "administratorLoginPassword": "[parameters('sqlAdminPassword')]",
        "version": "12.0",
        "minimalTlsVersion": "1.2",
        "publicNetworkAccess": "Enabled"
      },
      "resources": [
        {
          "type": "firewallRules",
          "apiVersion": "2022-05-01-preview",
          "name": "AllowAllAzureIPs",
          "dependsOn": [
            "[resourceId('Microsoft.Sql/servers', variables('sqlServerName'))]"
          ],
          "properties": {
            "startIpAddress": "0.0.0.0",
            "endIpAddress": "0.0.0.0"
          }
        }
      ]
    },
    {
      "type": "Microsoft.Sql/servers/databases",
      "apiVersion": "2022-05-01-preview",
      "name": "[concat(variables('sqlServerName'), '/', variables('databaseName'))]",
      "location": "[parameters('location')]",
      "tags": "[variables('allTags')]",
      "dependsOn": [
        "[resourceId('Microsoft.Sql/servers', variables('sqlServerName'))]"
      ],
      "sku": {
        "name": "[variables('currentEnvironmentConfig').databaseSku]"
      },
      "properties": {
        "maxSizeBytes": "[mul(parameters('databaseSizeGB'), 1073741824)]",
        "collation": "SQL_Latin1_General_CP1_CI_AS",
        "catalogCollation": "SQL_Latin1_General_CP1_CI_AS",
        "zoneRedundant": "[if(equals(parameters('environment'), 'prod'), true(), false())]",
        "readScale": "[if(equals(parameters('environment'), 'prod'), 'Enabled', 'Disabled')]"
      }
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    },
    "webAppName": {
      "type": "string",
      "value": "[variables('webAppName')]"
    },
    "webAppUrl": {
      "type": "string",
      "value": "[concat('https://', reference(resourceId('Microsoft.Web/sites', variables('webAppName'))).defaultHostName)]"
    },
    "sqlServerFqdn": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Sql/servers', variables('sqlServerName'))).fullyQualifiedDomainName')]"
    },
    "databaseName": {
      "type": "string",
      "value": "[variables('databaseName')]"
    },
    "applicationInsightsKey": {
      "type": "string",
      "value": "[if(parameters('enableApplicationInsights'), reference(resourceId('Microsoft.Insights/components', variables('appInsightsName'))).InstrumentationKey, 'Not enabled')]"
    },
    "resourceGroupLocation": {
      "type": "string",
      "value": "[resourceGroup().location]"
    },
    "deploymentTimestamp": {
      "type": "string",
      "value": "[utcNow()]"
    }
  }
}
```

### Parameter File for Development Environment

Create a file named `web-app-complete.parameters.dev.json`:
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "value": "webapp"
    },
    "environment": {
      "value": "dev"
    },
    "storageAccountType": {
      "value": "Standard_LRS"
    },
    "appServicePlanSku": {
      "value": {
        "name": "F1",
        "tier": "Free",
        "capacity": 1
      }
    },
    "enableApplicationInsights": {
      "value": true
    },
    "sqlAdminUsername": {
      "value": "sqladmin"
    },
    "sqlAdminPassword": {
      "reference": {
        "keyVault": {
          "id": "/subscriptions/{subscription-id}/resourceGroups/{rg-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
        },
        "secretName": "sqlAdminPassword"
      }
    },
    "databaseSizeGB": {
      "value": 2
    },
    "tags": {
      "value": {
        "CostCenter": "IT-Development",
        "Owner": "DevTeam"
      }
    }
  }
}
```
