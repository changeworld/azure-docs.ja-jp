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
	ms.date="03/04/2016" 
	ms.author="tomfitz"/>

# 新しいリソース グループまたはサブスクリプションへのリソースの移動

このトピックでは、リソースを 1 つのリソース グループから別のリソース グループに移動する方法を示します。新しいサブスクリプションにリソースを移動することもできます (ただし、サブスクリプションは同じ[テナント](./active-directory/active-directory-howto-tenant.md)内に存在する必要があります)。リソースを移動する必要があるのは、次のような場合です。

1. 課金の目的のために、リソースを異なるサブスクリプションで有効にする必要がある。
2. リソースが以前一緒にグループ化されていた他のリソースと、現在は同じライフサイクルを共有していない。そのリソースを他のリソースと別に管理できるように、新しいリソース グループに移動する必要があります。
3. リソースが現在、異なるリソース グループ内の他のリソースと同じライフサイクルを共有している。一緒に管理できるように、リソースを他のリソースと同じリソース グループに移動する必要があります。

## リソースの移動前の考慮事項

リソースを移動する前に、重要な考慮すべき問題がいくつかあります。

1. リソースの場所を変更することはできません。リソースを移動しても、新しいリソース グループに移動されるだけです。新しいリソース グループは別の場所に存在する場合もありますが、リソース自体の場所は変更されません。
2. 移動するリソースのリソース プロバイダーを移動先のサブスクリプションに登録する必要があります。この問題は、リソースを新しいサブスクリプションに移動するが、そのサブスクリプションがそのリソースの種類で使用されたことがない場合に発生する可能性があります。たとえば、**Microsoft.ApiManagement** リソース プロバイダーに登録されていないサブスクリプションに API Management サービス インスタンスを移動しようとすると、その移動は成功しません。登録ステータスを確認し、リソース プロバイダーを登録する方法については、「[リソース プロバイダーと種類](../resource-manager-supported-services/#resource-providers-and-types)」を参照してください。
2. 移動先のリソース グループには、移動するリソースと同じアプリケーション ライフサイクルを共有するリソースのみが含まれている必要があります。
3. Azure PowerShell または Azure CLI を使用している場合は、最新のバージョンを使用していることを確認します。使用しているバージョンを更新するには、Microsoft Web プラットフォーム インストーラーを実行し、新しいバージョンがあるかどうかを確認します。詳細については、「[Azure PowerShell のインストールと構成の方法](powershell-install-configure.md)」と「[Azure CLI のインストール](xplat-cli-install.md)」を参照してください。
4. 移動操作は完了までに時間がかかることがあります。その間、プロンプトは操作が完了するまで待機状態となります。
5. リソースを移動する場合は、その操作の間、ソース グループとターゲット グループの両方がロックされます。これらのグループに対する書き込み操作および削除操作は、移動が完了するまでブロックされます。

## サポートされているサービス

現時点では、すべてのサービスがリソースの移動機能をサポートしているわけではありません。

現在、新しいリソース グループへの移動と新しいサブスクリプションへの移動の両方をサポートするサービスは、次のとおりです。

- API Management
- App Service アプリ (後述の「[App Service の制限事項](#app-service-limitations)」を参照してください)
- Automation
- Batch  

- Data Factory
- DocumentDB
- HDInsight クラスター
- Key Vault
- Mobile Engagement
- Notification Hubs
- Operational Insights
- Redis Cache
- Search
- SQL Database サーバー (後述の「[SQL Database 制限事項](#sql-database-limitations)」を参照してください)

新しいリソース グループへの移動をサポートし、新しいサブスクリプションへの移動はサポートしないサービスは、次のとおりです。

- Virtual Machines (クラシック)
- Storage (クラシック)
- Virtual Networks
- Cloud Services

現在リソースの移動をサポートしていないサービスは、次のとおりです。

- Virtual Machines
- Storage
- ExpressRoute

## App Service の制限事項

App Service アプリを使用している場合、App Service プランのみを移動することはできません。App Service アプリを移動するには、次のオプションがあります。

- 移動先のリソース グループにまだ Microsoft.Web リソースがない場合は、すべてのリソースをあるリソース グループから別のリソース グループに移動します。
- Web アプリは別のリソース グループに移動しますが、App Service プランは元のリソース グループで保持します。

## SQL Database の制限事項

SQL データベースをそのサーバーから個別に移動することはできません。データベースとサーバーは、同じリソース グループ内に存在する必要があります。SQL Server を移動すると、そのデータベースもすべて移動されます。

## PowerShell を使用したリソースの移動

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、**Move-AzureRmResource** コマンドを使用します。

最初の例では、1 つのリソースを新しいリソース グループに移動する方法を示します。

    PS C:\> $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

2 番目の例では、複数のリソースを新しいリソース グループに移動する方法を示します。

    PS C:\> $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    PS C:\> $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

新しいサブスクリプションに移動する場合は、**DestinationSubscriptionId** パラメーターの値を含めます。

## Azure CLI を使用したリソースの移動

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、**azure resource move** コマンドを使用します。次の例は、Redis Cache を新しいリソース グループに移動する方法を示しています。 **-I** パラメーターには、移動するリソース ID のコンマ区切りリストを指定します。

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## REST API を使用したリソースの移動

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、次のコマンドを実行します。

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

要求の本文で、ターゲット リソース グループと、移動するリソースを指定します。REST による移動操作の詳細については、「[リソースの移動](https://msdn.microsoft.com/library/azure/mt218710.aspx)」を参照してください。

## ポータルを使用したリソースの移動

ポータル経由で一部のリソースを移動することができます。ただし、移動操作をサポートするすべてのリソース プロバイダーが、ポータル経由でその機能を提供しているわけではありません。

リソースを移動するには、リソースを選択し、**[移動]** ボタンを選択します。

![リソースの移動](./media/resource-group-move-resources/move-resources.png)

リソースを移動したい場所を指定します。そのリソースと一緒に他のリソースを移動する必要がある場合、それらが一覧表示されます。

![移動先の選択](./media/resource-group-move-resources/select-destination.png)

## 次のステップ
- [リソース マネージャーでの Azure PowerShell の使用](./powershell-azure-resource-manager.md)
- [リソース マネージャーでの Azure クロスプラットフォーム CLI の使用](./xplat-cli-azure-resource-manager.md)
- [Using the Azure Preview Portal to manage your Azure resources (Azure プレビュー ポータルを使用した Azure リソースの管理)](azure-portal/resource-group-portal.md)
- [タグを使用した Azure リソースの整理](./resource-group-using-tags.md)

<!---HONumber=AcomDC_0309_2016-->