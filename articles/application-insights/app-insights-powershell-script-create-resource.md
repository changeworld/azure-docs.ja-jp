<properties 
	pageTitle="Application Insights リソースを作成するための PowerShell スクリプト" 
	description="Application Insights リソースの作成を自動化します。" 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/17/2015" 
	ms.author="awills"/>

#  Application Insights リソースを作成するための PowerShell スクリプト

*Application Insights はプレビュー段階です。*

[Visual Studio Application Insights](https://azure.microsoft.com/services/application-insights/) を使って新しいアプリケーションを監視する際や、新しいバージョンのアプリケーションを監視する際、Microsoft Azure で新しいリソースをセットアップします。このリソースは、アプリのテレメトリ データを分析、表示する場所です。

You can automate the creation of a new resource by using PowerShell.

For example, if you are developing a mobile device app, it's likely that, at any time, there will be several published versions of your app in use by your customers.You don't want to get the telemetry results from different versions mixed up.それで、ビルド プロセスを取得して、各ビルドで新しいリソースを作成します。

## Application Insights リソースを作成するスクリプト

*出力*

* App Insights 名 = erimattestapp
* IKey = 00000000-0000-0000-0000-000000000000

*PowerShell スクリプト*

```PowerShell

cls


##################################################################
# Set Values
##################################################################

#If running manually, comment this out before the first execution to login to the Azure Portal
#Add-AzureAccount

#Set the name of the Application Insights Resource
$appInsightsName = "TestApp"

#Set the application name used for the value of the Tag "AppInsightsApp" - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

#Set the name of the Resource Group to use.  By default will use the application name as a starter
$resourceGroupName = "MyAppResourceGroup"

##################################################################
# Create the Resource and Output the name and iKey
##################################################################
#Set the script to Resource Manager - http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/
Switch-AzureMode AzureResourceManager

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

#Create the App Insights Resource
$resource = New-AzureResource -Name $appInsightsName -ResourceGroupName $resourceGroupName -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} -ResourceType "Microsoft.Insights/Components" -Location "Central US" -ApiVersion "2014-08-01" -PropertyObject @{"Type"="ASP.NET"} -Force 

#Give team owner access - http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/
New-AzureRoleAssignment -Mail "myTeam@fabrikam.com" -RoleDefinitionName Owner -Scope $resource.ResourceId | Out-Null

#Display iKey
Write-Host "App Insights Name = " $resource.Properties["Name"]
Write-Host "IKey = " $resource.Properties["InstrumentationKey"]

```

## iKey を使用して行うこと

各リソースは、インストルメンテーション キー (iKey) によって識別されます。The iKey is an output of the resource creation script.Your build script should provide the iKey to the Application Insights SDK embedded in your app.

iKey を SDK で使用できるようにする方法は 2 つあります。
  
* [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) の場合: 
 * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* [初期化コードの場合](app-insights-api-custom-events-metrics.md): 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`





 

<!---HONumber=Oct15_HO3-->