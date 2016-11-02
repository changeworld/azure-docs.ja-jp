<properties
    pageTitle="Azure Resource Manager-based Cross-platform Command Line Tools for Azure Web App | Microsoft Azure"
    description="Learn how to use the new Azure Resource Manager-based Cross-platform Command Line Tools to manage your Azure Web Apps."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="aelnably"/>


# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-web-app#"></a>Using Azure Resource Manager-Based XPlat CLI for Azure Web App#

> [AZURE.SELECTOR]
- [Azure CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)

With the release of Microsoft Azure Cross-platform Command-Line Tools version 0.10.5, new commands have been added. These commands give the user the ability to use Azure Resource Manager-based PowerShell commands to manage Web Apps.

To learn about managing Resource Groups, see [Use the Azure CLI to manage Azure resources and resource groups](../xplat-cli-azure-resource-manager.md). 


## <a name="managing-app-service-plans"></a>Managing App Service Plans ##

### <a name="create-an-app-service-plan"></a>Create an App Service Plan ###
To create an app service plan, use the **azure appserviceplan create** command.

Following are descriptions of the different parameters:

-   **--resource-group**: resource group that includes the newly created app service plan.
-   **--name**: name of the app service plan.
-   **--location**: app service plan location.
-   **--tier**:  the desired pricing sku (The options are: F1 (Free). D1 (Shared). B1 (Basic Small), B2 (Basic Medium), and B3 (Basic Large). S1 (Standard Small), S2 (Standard Medium), and S3 (Standard Large). P1 (Premium Small), P2 (Premium Medium), and P3 (Premium Large).)
-   **--instances**: the number of workers in the app service plan (Default value is 1).

Example to use this cmdlet:

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

### <a name="list-existing-app-service-plans"></a>List Existing App Service Plans ###

To list the existing app service plans, use **azure appserviceplan list** command.

To list all app service plans under a specific resource group, use:

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

To get a specific app service plan, use **azure appserviceplan show** command:

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>Configure an existing App Service Plan ###

To change the settings for an existing app service plan, use the **azure appserviceplan config** command. You can change the sku, and the number of workers 

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>Scaling an App Service Plan ####

To scale an existing App Service Plan, use:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>Changing the SKU of an App Service Plan ####

To change the sku of an existing App Service Plan, use:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>Delete an existing App Service Plan ###

To delete an existing app service plan, all assigned web apps need to be moved or deleted first. Then using the **azure webapp delete** command you can delete the app service plan.

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-web-apps"></a>Managing App Service Web Apps ##

### <a name="create-a-web-app"></a>Create a Web App ###

To create a web app, use the **azure webapp create** command.

Following are descriptions of the different parameters:

- **--name**: name for the web app.
- **--plan**: name for the service plan used to host the web app.
- **--resource-group**: resource group that hosts the App service plan.
- **--location**: the web app location.

Example to use this cmdlet:

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-web-app"></a>Delete an existing Web App ###

To delete an existing web app you can use the **azure webapp delete** command, you need to specify the name of the web app and the resource group name.

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-web-apps"></a>List existing Web Apps ###

To list the existing web apps, use the **azure webapp list** command.

To list all web apps under a specific resource group, use:

    azure webapp list --resource-group ContosoAzureResourceGroup

To get a specific web app, use the **azure webapp show** command.

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-web-app"></a>Configure an existing Web App ###

To change the settings and configurations for an existing web app, use the **azure webapp config set** command.

Example (1): change the php version of a web app 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

Example (2): add or change app settings

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

To know what other configuration can be changed, use the **azure webapp config set -h** command.

### <a name="change-the-state-of-an-existing-web-app"></a>Change the state of an existing Web App ###

#### <a name="restart-a-web-app"></a>Restart a web app ####

To restart a web app, you must specify the name and resource group of the web app.

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Stop a web app ####

To stop a web app, you must specify the name and resource group of the web app.

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Start a web app ####

To start a web app, you must specify the name and resource group of the web app.

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Manage Web App Publishing profiles ###

Each web app has a publishing profile that can be used to publish your apps.

#### <a name="get-publishing-profile"></a>Get Publishing Profile ####

To get the publishing profile for a web app, use:

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

This command echoes the publishing profile username and password to the command line.

### <a name="manage-web-app-hostnames"></a>Manage Web App hostnames ###

To manage hostname bindings for your web app, use the **azure webapp config hostnames** command  

#### <a name="list-hostname-bindings"></a>List hostname bindings ####

To get the current hostname bindings for a web app, use:

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>Add hostname bindings ####

To add hostname bindings to a web app, use:

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>Delete hostname bindings ####

To delete hostname bindings, use:

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

### <a name="next-steps"></a>Next Steps ###
- To learn about Azure Resource Manager CLI support, see [Use the Azure CLI to manage Azure resources and resource groups.](../xplat-cli-azure-resource-manager.md)
- To learn about managing App Service using PowerShell, see [Using Azure Resource Manager-Based PowerShell to Manage Azure Web Apps.](app-service-web-app-azure-resource-manager-powershell.md)



<!--HONumber=Oct16_HO2-->


