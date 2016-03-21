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
	ms.date="02/19/2016" 
	ms.author="awills"/>

#  Application Insights リソースを作成するための PowerShell スクリプト

*Application Insights はプレビュー段階です。*

[Visual Studio Application Insights](https://azure.microsoft.com/services/application-insights/) を使って新しいアプリケーションを監視する際や、新しいバージョンのアプリケーションを監視する際、Microsoft Azure で新しいリソースをセットアップします。このリソースは、アプリのテレメトリ データを分析、表示する場所です。

PowerShell を使用して、新しいリソースの作成を自動化できます。

たとえば、モバイル デバイス アプリを開発している場合、顧客によって使用されている発行済みのアプリのバージョンは常に複数存在する可能性があります。複数のバージョンから取得したテレメトリの結果を混在させないようにします。それで、ビルド プロセスを取得して、各ビルドで新しいリソースを作成します。

## Application Insights リソースを作成するスクリプト

関連するコマンドレットの仕様を参照してください。

* [New-AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)


*PowerShell スクリプト*

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Add-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 
# - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource

$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} `
  -ResourceType "Microsoft.Insights/Components" `
  -Location "Central US" `
  -PropertyObject @{"Type"="ASP.NET"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


#Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## iKey を使用して行うこと

各リソースは、インストルメンテーション キー (iKey) によって識別されます。iKey はリソース作成スクリプトの出力です。ビルド スクリプトで、アプリに組み込まれた Application Insights SDK に iKey を提供する必要があります。

iKey を SDK で使用できるようにする方法は 2 つあります。
  
* [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) の場合: 
 * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* [初期化コードの場合](app-insights-api-custom-events-metrics.md): 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`



## 関連項目

* [テンプレートから Application Insights と Web テスト リソースを作成する](app-insights-powershell.md)
* [PowerShell で Azure 診断の監視を設定する](app-insights-powershell-azure-diagnostics.md) 
* [PowerShell を使用したアラートの設定](app-insights-powershell-alerts.md)

 

<!---HONumber=AcomDC_0224_2016-->