<properties 
	pageTitle="新しいリソース グループへのリソースの移動" 
	description="Azure PowerShell または REST API を使用して、リソースを Azure リソース マネージャーの新しいリソース グループに移動します。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/14/2015" 
	ms.author="tomfitz"/>

# 新しいリソース グループまたはサブスクリプションへのリソースの移動

このトピックでは、リソースを 1 つのリソース グループから別のリソース グループに移動する方法を示します。リソースは、新しいサブスクリプションに移動することもできます。リソースを移動する必要があるのは、次のような場合です。

1. 課金の目的のために、リソースを異なるサブスクリプションで有効にする必要がある。
2. リソースが以前一緒にグループ化されていた他のリソースと、現在は同じライフサイクルを共有していない。そのリソースを他のリソースと別に管理できるように、新しいリソース グループに移動する必要があります。
3. リソースが現在、異なるリソース グループ内の他のリソースと同じライフサイクルを共有している。一緒に管理できるように、リソースを他のリソースと同じリソース グループに移動する必要があります。

リソースを移動するときには、重要な考慮事項がいくつかあります。

1. リソースの場所を変更することはできません。リソースを移動しても、新しいリソース グループに移動されるだけです。新しいリソース グループは別の場所に存在する場合もありますが、リソース自体の場所は変更されません。
2. 移動先のリソース グループには、移動するリソースと同じアプリケーション ライフサイクルを共有するリソースのみが含まれている必要があります。
3. Azure PowerShell を使用している場合は、最新のバージョンを使用していることを確認します。**Move-AzureResource** コマンドは、頻繁に更新されます。使用しているバージョンを更新するには、Microsoft Web プラットフォーム インストーラーを実行し、新しいバージョンがあるかどうかを確認します。詳細については、「[Azure PowerShell のインストールと構成の方法](powershell-install-configure.md)」を参照してください。
4. 移動操作は完了までに時間がかかることがあり、その間、PowerShell のプロンプトは、操作が完了するまで待機状態となります。

## サポートされているサービス

現時点では、すべてのサービスがリソースの移動機能をサポートしているわけではありません。

現在、新しいリソース グループへの移動と新しいサブスクリプションへの移動の両方をサポートするサービスは、次のとおりです。

- API Management
- Azure DocumentDB
- Azure Search
- Azure Web Apps (いくつかの [制限](app-service-web/app-service-move-resources.md)が適用されます)
- Data Factory
- Key Vault
- Mobile Engagement
- Operational Insights
- Redis Cache
- SQL Database

新しいリソース グループへの移動をサポートし、新しいサブスクリプションへの移動はサポートしないサービスは、次のとおりです。

- Virtual Machines (クラシック)
- Storage (クラシック)

現在リソースの移動をサポートしていないサービスは、次のとおりです。

- Virtual Machines
- Virtual Networks

Web アプリを使用している場合、App Service プランのみを移動することはできません。Web アプリを移動するには、次のオプションがあります。

- 移動先のリソース グループにまだ Microsoft.Web リソースがない場合は、すべてのリソースをあるリソース グループから別のリソース グループに移動します。
- Web アプリは別のリソース グループに移動しますが、App Service プランは元のリソース グループで保持します。

## PowerShell を使用したリソースの移動

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、**Move-AzureRmResource** コマンドを使用します。

最初の例では、1 つのリソースを新しいリソース グループに移動する方法を示します。

    PS C:\> Move-AzureRmResource -DestinationResourceGroupName TestRG -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OtherExample/providers/Microsoft.ClassicStorage/storageAccounts/examplestorage

2 番目の例では、複数のリソースを新しいリソース グループに移動する方法を示します。

    PS C:\> $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite -ResourceType Microsoft.Web/sites
    PS C:\> $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan -ResourceType Microsoft.Web/serverFarms
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId ($webapp.ResourceId, $plan.ResourceId)

新しいサブスクリプションに移動する場合は、**DestinationSubscriptionId** パラメーターの値を含めます。

## REST API を使用したリソースの移動

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、次のコマンドを実行します。

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

ここで、**{source-subscription-id}** と **{source-resource-group-name}** は、移動するリソースが現在含まれているサブスクリプションとリソース グループで置き換えます。{api-version} には、**2015-01-01** を使用します。

要求には、移動先のリソース グループと移動するリソースとを定義する JSON オブジェクトを含めます。

    {
        "targetResourceGroup": "/subscriptions/{target-subscription-id}/resourceGroups/{target-resource-group-name}", "resources": [
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}"
        ]
    }

## 次のステップ
- [リソース マネージャーでの Azure PowerShell の使用](./powershell-azure-resource-manager.md)
- [リソース マネージャーでの Azure クロスプラットフォーム CLI の使用](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Using the Azure Preview Portal to manage your Azure resources (Azure プレビュー ポータルを使用した Azure リソースの管理)](azure-portal/resource-group-portal.md)
- [タグを使用した Azure リソースの整理](./resource-group-using-tags.md)

<!---HONumber=Oct15_HO3-->