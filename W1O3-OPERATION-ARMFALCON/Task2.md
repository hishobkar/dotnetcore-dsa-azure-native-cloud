# Task 2: Create and Manage a Resource Group in Azure Portal

## Overview
This task focuses on creating and managing Azure Resource Groups through the Azure Portal, understanding their properties, applying tags for organization, and exploring various management options including viewing, deleting, and locking resources.

## Prerequisites
- Active Azure subscription
- Access to Azure Portal (https://portal.azure.com)
- Appropriate permissions to create and manage resource groups (Contributor or Owner role)
- Completed Task 1: Introduction to Azure Resource Manager (ARM)
- Basic understanding of Azure Resource Manager concepts

## Estimated Time
**30 minutes**

## Learning Objectives
By the end of this task, you should be able to:
1. Create a resource group using Azure Portal
2. Understand resource group properties and settings
3. Apply and manage tags for resource organization
4. View and monitor resources within a resource group
5. Implement resource locks to prevent accidental deletion
6. Delete resource groups and understand the implications
7. Understand how to replicate these operations in .NET Core applications

## What is a Resource Group?

A **Resource Group** is a logical container for resources deployed within Azure. It acts as a boundary for:
- **Access control**: Apply permissions at the resource group level
- **Lifecycle management**: Deploy, update, and delete resources together
- **Billing**: Track costs for grouped resources
- **Organization**: Logically group related resources

### Key Characteristics
- Every resource must exist in one and only one resource group
- Resource groups cannot be nested
- Resources can communicate across resource groups
- Resources in a resource group can be in different Azure regions
- Resource group location stores metadata, not the resources themselves
- Resource groups can be moved or renamed

## Part 1: Creating a Resource Group in Azure Portal

### Step-by-Step Instructions

#### Step 1: Sign in to Azure Portal
1. Navigate to https://portal.azure.com
2. Sign in with your Azure account credentials
3. Verify you're in the correct subscription (top-right corner)

#### Step 2: Navigate to Resource Groups
1. Click on the **hamburger menu** (☰) in the top-left corner
2. Select **Resource groups** from the left navigation menu
3. Or use the search bar at the top and type "Resource groups"

#### Step 3: Create New Resource Group
1. Click the **+ Create** or **Add** button at the top
2. You'll see the "Create a resource group" form

#### Step 4: Configure Basic Settings

Fill in the following details:

**Subscription**
- Select your Azure subscription from the dropdown
- Example: `Visual Studio Enterprise Subscription`

**Resource group**
- Enter a unique name for your resource group
- Naming convention: `rg-[project]-[environment]-[region]`
- Example: `rg-dotnetapp-dev-eastus`
- Restrictions: 
  - 1-90 characters
  - Alphanumeric, underscores, hyphens, periods, and parentheses
  - Cannot end with a period

**Region**
- Select the Azure region where metadata will be stored
- Example: `East US`, `West Europe`, `Southeast Asia`
- Note: This doesn't restrict where resources can be deployed
- Consider: Compliance, latency, and pricing

#### Step 5: Add Tags (Important for Organization)

Tags are name-value pairs that help organize and categorize resources.

Click on **Next: Tags** or navigate to the Tags tab.

Add the following recommended tags:

| Tag Name | Tag Value | Purpose |
|----------|-----------|---------|
| Environment | Development | Identify deployment environment |
| Project | DotNetCoreApp | Project identification |
| Owner | your.email@company.com | Resource ownership |
| CostCenter | IT-Development | Billing and cost tracking |
| CreatedDate | 2025-11-01 | Audit and lifecycle management |
| ManagedBy | Portal | Creation method |

**Tag Best Practices:**
- Use consistent naming across your organization
- Apply tags at the resource group level (inherited by resources)
- Maximum 50 tags per resource group
- Tag names: up to 512 characters
- Tag values: up to 256 characters
- Tags are case-insensitive for operations

#### Step 6: Review and Create
1. Click **Next: Review + create**
2. Review all settings:
   - Subscription
   - Resource group name
   - Region
   - Tags
3. Ensure validation passes (green checkmark)
4. Click **Create**

#### Step 7: Verify Creation
1. Wait for the deployment notification (usually instant)
2. Click **Go to resource group** or navigate to Resource groups
3. Verify your new resource group appears in the list

### Screenshot Reference Points
- Resource group creation form
- Tags configuration section
- Successful deployment notification
- Resource group overview page

## Part 2: Exploring Resource Group Management Options

### 2.1 Resource Group Overview Page

After creating your resource group, explore the overview page:

**Left Navigation Menu:**
- Overview
- Activity log
- Access control (IAM)
- Tags
- Settings
- Deployments
- Policies
- Properties
- Locks
- Export template

**Top Action Bar:**
- Delete resource group
- Move
- Refresh
- Add resource
- Assign tags
- Create alert rule

### 2.2 Viewing Resource Group Properties

1. Click on **Properties** in the left menu
2. Review the following information:

| Property | Description | Example Value |
|----------|-------------|---------------|
| Resource Group | Name of the resource group | rg-dotnetapp-dev-eastus |
| Subscription | Associated subscription | Visual Studio Enterprise |
| Subscription ID | Unique subscription identifier | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx |
| Resource ID | Full ARM resource identifier | /subscriptions/{id}/resourceGroups/{name} |
| Location | Metadata storage region | eastus |
| Provisioning State | Current state | Succeeded |

### 2.3 Viewing and Managing Tags

#### View Current Tags
1. Click on **Tags** in the left menu
2. View all applied tags
3. See which resources inherit these tags

#### Add or Modify Tags
1. Click **+ Add** or edit existing tag values
2. Enter Name and Value
3. Click **Save**

#### Remove Tags
1. Click the trash icon next to the tag
2. Confirm deletion
3. Click **Save**

#### Bulk Tag Management
```plaintext
Name : Value
Environment : Production
Project : DotNetCoreApp
Owner : john.doe@company.com
Department : Engineering
```

### 2.4 Access Control (IAM)

Manage who can access and manage the resource group.

#### View Role Assignments
1. Click **Access control (IAM)** in the left menu
2. Click **Role assignments** tab
3. Review existing assignments

#### Add Role Assignment
1. Click **+ Add** → **Add role assignment**
2. Select a role:
   - **Owner**: Full access including access management
   - **Contributor**: Full access except access management
   - **Reader**: Read-only access
3. Select members (users, groups, service principals)
4. Click **Review + assign**

#### Common Roles for .NET Core Development
- **Contributor**: Developers who deploy resources
- **Reader**: Auditors or monitoring tools
- **Owner**: Team leads or administrators
- **Custom roles**: Specific permission sets

### 2.5 Activity Log

Monitor all operations performed on the resource group.

1. Click **Activity log** in the left menu
2. View operations:
   - Create resource group
   - Add tags
   - Role assignments
   - Resource deployments
   - Delete operations

#### Filter Activity Log
- **Timespan**: Last hour, 24 hours, 7 days, custom
- **Resource type**: All, specific types
- **Operation**: Create, Delete, Write, Action
- **Status**: Succeeded, Failed, All

#### Export Activity Logs
1. Click **Download as CSV** for offline analysis
2. Or set up **Diagnostic settings** for long-term retention

## Part 3: Implementing Resource Locks

Resource locks prevent accidental deletion or modification of critical resources.

### Types of Locks

#### 1. Read-Only Lock
- Prevents any modifications
- Users can read but not delete or update
- Similar to Reader role applied to all users

#### 2. Delete Lock (CanNotDelete)
- Prevents deletion
- Allows read and modify operations
- Recommended for production environments

### Creating a Resource Lock

#### Step 1: Navigate to Locks
1. In your resource group, click **Locks** in the left menu
2. Click **+ Add** at the top

#### Step 2: Configure Lock
**Lock name**: Enter a descriptive name
- Example: `prevent-delete-production`

**Lock type**: Select lock level
- **Read-only**: No modifications allowed
- **Delete**: Deletion prevented, modifications allowed

**Notes**: Add explanation for the lock
- Example: "Production environment - prevent accidental deletion"

#### Step 3: Create Lock
1. Click **OK**
2. Lock is applied immediately
3. Verify lock appears in the list

### Testing Resource Locks

#### Test Delete Lock
1. Try to delete the resource group
2. You should see an error message:
```
   Failed to delete resource group 'rg-dotnetapp-dev-eastus'
   Scope '/subscriptions/{id}/resourceGroups/rg-dotnetapp-dev-eastus' cannot perform delete operation because following scope(s) are locked: '/subscriptions/{id}/resourceGroups/rg-dotnetapp-dev-eastus'. Please remove lock and try again.
```

#### Remove Lock (When Necessary)
1. Navigate to **Locks**
2. Click the lock you want to remove
3. Click **Delete**
4. Confirm deletion

### Lock Best Practices
- Apply delete locks to production resource groups
- Document lock purpose in notes field
- Use consistent naming for locks
- Review locks periodically
- Remove locks only when necessary with proper approval

## Part 4: Viewing Resources and Deployments

### 4.1 Overview Tab

The Overview tab shows:
- **Essentials**: Basic information (subscription, location, tags)
- **Resources**: List of all resources in the group
- **Deployments**: Recent deployment history
- **Activity log**: Recent operations

### 4.2 Adding Resources

1. Click **+ Create a resource** in the overview
2. Or use **Add** button and search for resource type
3. Common resources for .NET Core apps:
   - App Service (Web Apps)
   - SQL Database
   - Storage Account
   - Key Vault
   - Application Insights

### 4.3 Viewing Deployments

1. Click **Deployments** in the left menu
2. View deployment history:
   - Deployment name
   - Status (Succeeded, Failed, Running)
   - Timestamp
   - Duration

3. Click on a deployment to see:
   - Inputs (parameters)
   - Outputs
   - Template used
   - Operation details
   - Related events

### 4.4 Export Template

1. Click **Export template** in the left menu
2. View the ARM template that represents the resource group
3. Options:
   - **Download**: Save template locally
   - **Add to library**: Save to template library
   - **Deploy**: Redeploy the template

## Part 5: Deleting a Resource Group

### Before Deleting - Important Considerations

⚠️ **WARNING**: Deleting a resource group is permanent and irreversible!

**What gets deleted:**
- All resources within the resource group
- All nested configurations
- All data stored in the resources
- Deployment history
- Activity logs

**What is NOT deleted:**
- Resources in other resource groups
- Azure AD objects
- Shared resources outside the group

### Deletion Process

#### Step 1: Remove Locks
1. Navigate to **Locks**
2. Delete any existing locks
3. Verify locks are removed

#### Step 2: Backup Critical Data
- Export configuration templates
- Backup databases
- Download important files
- Document settings

#### Step 3: Delete Resource Group
1. Go to the resource group overview
2. Click **Delete resource group** at the top
3. Or use the ellipsis (...) menu and select Delete

#### Step 4: Confirm Deletion
1. Type the resource group name exactly to confirm
```
   rg-dotnetapp-dev-eastus
```
2. Optionally select:
   - ☑ **Apply force delete for Virtual Machines and Virtual Machine Scale Sets**
3. Click **Delete**

#### Step 5: Monitor Deletion
1. Deletion starts immediately
2. Notification appears in the top-right
3. Can take several minutes depending on resources
4. Refresh to verify deletion completion

### Deletion Failures

If deletion fails:
1. Check activity log for error details
2. Common causes:
   - Active locks
   - Resource dependencies
   - Insufficient permissions
   - Resources in use

## Part 6: .NET Core Implementation

Now that you understand portal operations, here's how to replicate them in .NET Core.

### Project Setup
```bash
dotnet new console -n ResourceGroupManager
cd ResourceGroupManager
dotnet add package Azure.Identity
dotnet add package Azure.ResourceManager
dotnet add package Azure.ResourceManager.Resources
dotnet add package Microsoft.Extensions.Configuration
dotnet add package Microsoft.Extensions.Configuration.Json
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
  "ResourceGroup": {
    "Name": "rg-dotnetapp-dev-eastus",
    "Location": "eastus",
    "Tags": {
      "Environment": "Development",
      "Project": "DotNetCoreApp",
      "Owner": "your.email@company.com",
      "CostCenter": "IT-Development",
      "ManagedBy": "DotNetCore"
    }
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
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

namespace ResourceGroupManager
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
                // Load configuration
                LoadConfiguration();

                // Initialize Azure clients
                await InitializeAzureClient();

                // Menu system
                await RunMenuAsync();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine($"Error: {ex.Message}");
                Console.ResetColor();
            }
        }

        static void LoadConfiguration()
        {
            _configuration = new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
                .Build();
        }

        static async Task InitializeAzureClient()
        {
            // Use DefaultAzureCredential for local development
            // It will try multiple authentication methods
            var credential = new DefaultAzureCredential();

            // Alternative: Use ClientSecretCredential for service principal
            // var credential = new ClientSecretCredential(
            //     _configuration["Azure:TenantId"],
            //     _configuration["Azure:ClientId"],
            //     _configuration["Azure:ClientSecret"]
            // );

            _armClient = new ArmClient(credential);

            string subscriptionId = _configuration["Azure:SubscriptionId"];
            _subscription = _armClient.GetSubscriptionResource(
                new ResourceIdentifier($"/subscriptions/{subscriptionId}")
            );

            Console.WriteLine($"Connected to subscription: {_subscription.Data.DisplayName}");
        }

        static async Task RunMenuAsync()
        {
            while (true)
            {
                Console.WriteLine("\n=== Azure Resource Group Manager ===");
                Console.WriteLine("1. Create Resource Group");
                Console.WriteLine("2. List Resource Groups");
                Console.WriteLine("3. Get Resource Group Details");
                Console.WriteLine("4. Update Resource Group Tags");
                Console.WriteLine("5. Create Resource Lock");
                Console.WriteLine("6. List Resource Locks");
                Console.WriteLine("7. Delete Resource Lock");
                Console.WriteLine("8. Delete Resource Group");
                Console.WriteLine("9. Exit");
                Console.Write("\nSelect an option: ");

                string choice = Console.ReadLine();

                try
                {
                    switch (choice)
                    {
                        case "1":
                            await CreateResourceGroupAsync();
                            break;
                        case "2":
                            await ListResourceGroupsAsync();
                            break;
                        case "3":
                            await GetResourceGroupDetailsAsync();
                            break;
                        case "4":
                            await UpdateResourceGroupTagsAsync();
                            break;
                        case "5":
                            await CreateResourceLockAsync();
                            break;
                        case "6":
                            await ListResourceLocksAsync();
                            break;
                        case "7":
                            await DeleteResourceLockAsync();
                            break;
                        case "8":
                            await DeleteResourceGroupAsync();
                            break;
                        case "9":
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
                    Console.WriteLine($"Azure Error: {ex.Message}");
                    Console.WriteLine($"Error Code: {ex.ErrorCode}");
                    Console.ResetColor();
                }
                catch (Exception ex)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine($"Error: {ex.Message}");
                    Console.ResetColor();
                }
            }
        }

        // 1. Create Resource Group
        static async Task CreateResourceGroupAsync()
        {
            Console.WriteLine("\n=== Create Resource Group ===");

            string resourceGroupName = _configuration["ResourceGroup:Name"];
            string location = _configuration["ResourceGroup:Location"];

            Console.WriteLine($"Creating resource group: {resourceGroupName}");
            Console.WriteLine($"Location: {location}");

            // Create resource group data with tags
            var resourceGroupData = new ResourceGroupData(new AzureLocation(location));

            // Add tags from configuration
            var tagsSection = _configuration.GetSection("ResourceGroup:Tags");
            foreach (var tag in tagsSection.GetChildren())
            {
                resourceGroupData.Tags.Add(tag.Key, tag.Value);
            }

            // Create the resource group
            var resourceGroupOperation = await _subscription.GetResourceGroups()
                .CreateOrUpdateAsync(
                    WaitUntil.Completed,
                    resourceGroupName,
                    resourceGroupData
                );

            var resourceGroup = resourceGroupOperation.Value;

            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine($"✓ Resource group '{resourceGroup.Data.Name}' created successfully!");
            Console.ResetColor();
            Console.WriteLine($"  Location: {resourceGroup.Data.Location}");
            Console.WriteLine($"  Resource ID: {resourceGroup.Data.Id}");
            Console.WriteLine($"  Provisioning State: {resourceGroup.Data.ProvisioningState}");
            Console.WriteLine($"  Tags: {resourceGroup.Data.Tags.Count}");
        }

        // 2. List Resource Groups
        static async Task ListResourceGroupsAsync()
        {
            Console.WriteLine("\n=== List Resource Groups ===");

            var resourceGroups = _subscription.GetResourceGroups().GetAllAsync();

            int count = 0;
            await foreach (var rg in resourceGroups)
            {
                count++;
                Console.WriteLine($"\n{count}. {rg.Data.Name}");
                Console.WriteLine($"   Location: {rg.Data.Location}");
                Console.WriteLine($"   Provisioning State: {rg.Data.ProvisioningState}");
                Console.WriteLine($"   Tags: {string.Join(", ", rg.Data.Tags.Select(t => $"{t.Key}={t.Value}"))}");
            }

            Console.WriteLine($"\nTotal resource groups: {count}");
        }

        // 3. Get Resource Group Details
        static async Task GetResourceGroupDetailsAsync()
        {
            Console.Write("\nEnter resource group name: ");
            string resourceGroupName = Console.ReadLine();

            var resourceGroupResponse = await _subscription.GetResourceGroups()
                .GetAsync(resourceGroupName);

            var resourceGroup = resourceGroupResponse.Value;

            Console.WriteLine("\n=== Resource Group Details ===");
            Console.WriteLine($"Name: {resourceGroup.Data.Name}");
            Console.WriteLine($"Location: {resourceGroup.Data.Location}");
            Console.WriteLine($"Resource ID: {resourceGroup.Data.Id}");
            Console.WriteLine($"Provisioning State: {resourceGroup.Data.ProvisioningState}");
            Console.WriteLine($"Managed By: {resourceGroup.Data.ManagedBy ?? "Not managed"}");

            Console.WriteLine("\nTags:");
            foreach (var tag in resourceGroup.Data.Tags)
            {
                Console.WriteLine($"  {tag.Key}: {tag.Value}");
            }

            // Get resources in the resource group
            var resources = resourceGroup.GetGenericResources().GetAllAsync();
            var resourceList = new List<string>();

            await foreach (var resource in resources)
            {
                resourceList.Add(resource.Data.ResourceType.ToString());
            }

            Console.WriteLine($"\nResources in group: {resourceList.Count}");
            foreach (var resourceType in resourceList.Distinct())
            {
                int typeCount = resourceList.Count(r => r == resourceType);
                Console.WriteLine($"  {resourceType}: {typeCount}");
            }
        }

        // 4. Update Resource Group Tags
        static async Task UpdateResourceGroupTagsAsync()
        {
            Console.Write("\nEnter resource group name: ");
            string resourceGroupName = Console.ReadLine();

            var resourceGroupResponse = await _subscription.GetResourceGroups()
                .GetAsync(resourceGroupName);

            var resourceGroup = resourceGroupResponse.Value;

            Console.WriteLine("\nCurrent Tags:");
            foreach (var tag in resourceGroup.Data.Tags)
            {
                Console.WriteLine($"  {tag.Key}: {tag.Value}");
            }

            Console.Write("\nEnter tag name to add/update: ");
            string tagName = Console.ReadLine();

            Console.Write("Enter tag value: ");
            string tagValue = Console.ReadLine();

            // Create update data
            var updateData = new ResourceGroupData(resourceGroup.Data.Location);
            
            // Copy existing tags
            foreach (var tag in resourceGroup.Data.Tags)
            {
                updateData.Tags.Add(tag.Key, tag.Value);
            }

            // Add or update the new tag
            updateData.Tags[tagName] = tagValue;

            // Update the resource group
            var updateOperation = await _subscription.GetResourceGroups()
                .CreateOrUpdateAsync(
                    WaitUntil.Completed,
                    resourceGroupName,
                    updateData
                );

            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine($"✓ Tag '{tagName}' updated successfully!");
            Console.ResetColor();
        }

        // 5. Create Resource Lock
        static async Task CreateResourceLockAsync()
        {
            Console.Write("\nEnter resource group name: ");
            string resourceGroupName = Console.ReadLine();

            var resourceGroupResponse = await _subscription.GetResourceGroups()
                .GetAsync(resourceGroupName);

            var resourceGroup = resourceGroupResponse.Value;

            Console.WriteLine("\nSelect lock type:");
            Console.WriteLine("1. CanNotDelete (Prevent deletion, allow modifications)");
            Console.WriteLine("2. ReadOnly (Prevent all modifications)");
            Console.Write("Choice: ");
            string lockTypeChoice = Console.ReadLine();

            ManagementLockLevel lockLevel = lockTypeChoice == "2" 
                ? ManagementLockLevel.ReadOnly 
                : ManagementLockLevel.CanNotDelete;

            Console.Write("Enter lock name: ");
            string lockName = Console.ReadLine();

            Console.Write("Enter lock notes/description: ");
            string notes = Console.ReadLine();

            var lockData = new ManagementLockData(lockLevel)
            {
                Notes = notes
            };

            var lockOperation = await resourceGroup.GetManagementLocks()
                .CreateOrUpdateAsync(
                    WaitUntil.Completed,
                    lockName,
                    lockData
                );

            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine($"✓ Lock '{lockName}' created successfully!");
            Console.ResetColor();
            Console.WriteLine($"  Type: {lockLevel}");
            Console.WriteLine($"  Notes: {notes}");
        }

        // 6. List Resource Locks
        static async Task ListResourceLocksAsync()
        {
            Console.Write("\nEnter resource group name: ");
            string resourceGroupName = Console.ReadLine();

            var resourceGroupResponse = await _subscription.GetResourceGroups()
                .GetAsync(resourceGroupName);

            var resourceGroup = resourceGroupResponse.Value;

            Console.WriteLine("\n=== Resource Locks ===");

            var locks = resourceGroup.GetManagementLocks().GetAllAsync();
            int count = 0;

            await foreach (var lockResource in locks)
            {
                count++;
                Console.WriteLine($"\n{count}. {lockResource.Data.Name}");
                Console.WriteLine($"   Level: {lockResource.Data.Level}");
                Console.WriteLine($"   Notes: {lockResource.Data.Notes ?? "No notes"}");
                Console.WriteLine($"   ID: {lockResource.Data.Id}");
            }

            if (count == 0)
            {
                Console.WriteLine("No locks found on this resource group.");
            }
            else
            {
                Console.WriteLine($"\nTotal locks: {count}");
            }
        }

        // 7. Delete Resource Lock
        static async Task DeleteResourceLockAsync()
        {
            Console.Write("\nEnter resource group name: ");
            string resourceGroupName = Console.ReadLine();

            var resourceGroupResponse = await _subscription.GetResourceGroups()
                .GetAsync(resourceGroupName);

            var resourceGroup = resourceGroupResponse.Value;

            Console.Write("Enter lock name to delete: ");
            string lockName = Console.ReadLine();

            var lockResponse = await resourceGroup.GetManagementLocks()
                .GetAsync(lockName);

            var lockResource = lockResponse.Value;

            Console.Write($"Are you sure you want to delete lock '{lockName}'? (yes/no): ");
            string confirmation = Console.ReadLine();

            if (confirmation?.ToLower() == "yes")
            {
                await lockResource.DeleteAsync(WaitUntil.Completed);

                Console.ForegroundColor = ConsoleColor.Green;
                Console.WriteLine($"✓ Lock '{lockName}' deleted successfully!");
                Console.ResetColor();
            }
            else
            {
                Console.WriteLine("Delete operation cancelled.");
            }
        }

        // 8. Delete Resource Group
        static async Task DeleteResourceGroupAsync()
        {
            Console.Write("\nEnter resource group name to delete: ");
            string resourceGroupName = Console.ReadLine();

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("\n⚠ WARNING: This will permanently delete the resource group and ALL resources within it!");
            Console.ResetColor();

            Console.Write($"Type the resource group name '{resourceGroupName}' to confirm: ");
            string confirmation = Console.ReadLine();

            if (confirmation != resourceGroupName)
            {
                Console.WriteLine("Resource group name does not match. Delete cancelled.");
                return;
            }

            var resourceGroupResponse = await _subscription.GetResourceGroups()
                .GetAsync(resourceGroupName);

            var resourceGroup = resourceGroupResponse.Value;

            Console.WriteLine("\nDeleting resource group...");

            await resourceGroup.DeleteAsync(WaitUntil.Completed);

            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine($"✓ Resource group '{resourceGroupName}' deleted successfully!");
            Console.ResetColor();
        }
    }
}
```

### Running the Application
```bash
# Build the project
dotnet build

# Run the application
dotnet run
```

## Hands-On Exercise

### Exercise 1: Portal Operations (15 minutes)

1. **Create a Development Resource Group**
   - Name: `rg-exercise-dev-[yourname]`
   - Region: `East US`
   - Tags:
     - Environment: Development
     - Owner: Your Name
     - Purpose: Training

2. **Apply a Delete Lock**
   - Lock name: `prevent-delete-dev`
   - Type: CanNotDelete
   - Notes: "Training resource group"

3. **Try to Delete the Resource Group**
   - Observe the error message
   - Document the behavior

4. **Remove the Lock and Delete**
   - Remove the lock
   - Successfully delete the resource group

### Exercise 2: .NET Core Implementation (15 minutes)

1. **Create the Console Application**
   - Set up project with required packages
   - Configure authentication

2. **Implement Create and List Operations**
   - Create a resource group programmatically
   - List all resource groups
   - Display details with tags

3. **Test Resource Lock Operations**
   - Create a delete lock via code
   - Verify in Azure Portal
   - Delete lock programmatically

## Best Practices Summary

### Naming Conventions
```
Resource Group: rg-[workload]-[environment]-[region]-[instance]
Example: rg-webapp-prod-eastus-001
```

### Tagging Strategy
- **Environment**: Production, Development, Staging, Testing
- **Owner**: Email or team name
- **CostCenter**: Department or cost code
- **Project**: Project or application name
- **Criticality**: High, Medium, Low
- **DataClassification**: Public, Internal, Confidential, Restricted

### Resource Group Organization
- Separate resource groups by environment
- Group resources with the same lifecycle
- Consider separate resource groups for shared services
- Use consistent naming across your organization

### Security Best Practices
- Use RBAC to control access
- Apply locks to production resource groups
- Regular audit of access and resources
- Use Azure Policy for governance
- Enable activity log monitoring

## Troubleshooting

### Common Issues

#### Issue 1: Cannot Create Resource Group
**Error**: `Authorization failed`
**Solution**: Ensure you have Contributor or Owner role at subscription level

#### Issue 2: Cannot Delete Resource Group
**Error**: `Scope is locked`
**Solution**: Remove all locks before attempting deletion

#### Issue 3: Tag Limit Exceeded
**Error**: `The resource has too many tags`
**Solution**: Maximum 50 tags per resource; remove unnecessary tags

#### Issue 4: .NET Core Authentication Failed
**Error**: `DefaultAzureCredential failed to retrieve a token`
**Solution**: 
- Run `az login` for Azure CLI authentication
- Or configure service principal credentials
- Ensure proper permissions are assigned

## Summary

In this task, you learned:
- ✓ How to create resource groups in Azure Portal
- ✓ How to apply and manage tags for organization
- ✓ How to view resource group properties and resources
- ✓ How to implement resource locks to prevent accidental deletion
- ✓ How to safely delete resource groups
- ✓ How to replicate all portal operations in .NET Core
- ✓ Best practices for resource group management
