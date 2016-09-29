<properties 
	pageTitle="新しいリソース グループへのリソースの移動 | Microsoft Azure" 
	description="Azure Resource Manager を使用して、リソースを新しいリソース グループまたはサブスクリプションに移動します。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="tomfitz"/>

# 新しいリソース グループまたはサブスクリプションへのリソースの移動

このトピックでは、リソースを 1 つのリソース グループから別のリソース グループに移動する方法を示します。新しいサブスクリプションにリソースを移動することもできます (ただし、サブスクリプションは同じ[テナント](./active-directory/active-directory-howto-tenant.md)内に存在する必要があります)。リソースを移動する必要があるのは、次のような場合です。

1. 課金の目的のために、リソースを異なるサブスクリプションで有効にする必要がある。
2. リソースが以前一緒にグループ化されていた他のリソースと、現在は同じライフサイクルを共有していない。そのリソースを他のリソースと別に管理できるように、新しいリソース グループに移動する必要があります。

リソースを移動する場合は、その操作の間、ソース グループとターゲット グループの両方がロックされます。これらのグループに対する書き込み操作および削除操作は、移動が完了するまでブロックされます。

リソースの場所を変更することはできません。リソースを移動しても、新しいリソース グループに移動されるだけです。新しいリソース グループは別の場所に存在する場合もありますが、リソース自体の場所は変更されません。

> [AZURE.NOTE] この記事では、既存の Azure アカウント プラン内のリソースを移動する方法について説明します。実際に Azure アカウント プランを変更 (従量課金制から前払いにアップグレードするなど) しながら既存のリソースの処理を継続する場合は、[別の Azure サブスクリプション プランへの切り替え](billing-how-to-switch-azure-offer.md)に関するページをご覧ください。

## リソースの移動前のチェック リスト

リソースを移動する前に実行すべき重要な手順がいくつかあります。これらの条件を確認することにより、エラーの発生を回避できます。

1. サービスでリソースの移動機能をサポートしている必要があります。[リソースの移動をサポートしているサービス](#services-that-support-move)については、下記の一覧を参照してください。
2. 移動するリソースのリソース プロバイダーについて、移動先のサブスクリプションに登録する必要があります。登録しないと、**リソースの種類についてサブスクリプションへの登録が行われていない**ことを示すエラーが発生します。この問題は、リソースを新しいサブスクリプションに移動するが、そのサブスクリプションがそのリソースの種類で使用されたことがない場合に発生する可能性があります。登録ステータスを確認し、リソース プロバイダーを登録する方法については、「[リソース プロバイダーと種類](../resource-manager-supported-services.md#resource-providers-and-types)」を参照してください。
3. Azure PowerShell または Azure CLI を使用している場合は、最新バージョンを使用してください。使用しているバージョンを更新するには、Microsoft Web プラットフォーム インストーラーを実行し、新しいバージョンがあるかどうかを確認します。詳細については、「[Azure PowerShell のインストールと構成の方法](powershell-install-configure.md)」と「[Azure CLI のインストール](xplat-cli-install.md)」を参照してください。
4. App Service アプリを移動する場合は、「[App Service の制限事項](#app-service-limitations)」を確認しておく必要があります。
5. クラシック モデルを使用してデプロイされたリソースを移動する場合は、「[クラシック デプロイメントの制限事項](#classic-deployment-limitations)」を確認しておく必要があります。

## 移動をサポートするサービス

現在、新しいリソース グループへの移動と新しいサブスクリプションへの移動の両方をサポートするサービスは、次のとおりです。

- API Management
- App Service アプリ (Web Apps) - 「[App Service の制限事項](#app-service-limitations)」を参照してください
- Automation
- Batch  

- CDN
- Cloud Services - 「[クラシック デプロイメントの制限事項](#classic-deployment-limitations)」を参照してください
- Data Factory
- DNS
- DocumentDB
- HDInsight クラスター
- Key Vault
- Media Services
- Mobile Engagement
- Notification Hubs
- Operational Insights
- Redis Cache
- Scheduler
- Search
- Storage
- Storage (クラシック) - 「[クラシック デプロイメントの制限事項](#classic-deployment-limitations)」を参照してください
- SQL Database サーバー - データベースとサーバーは同じリソース グループ内に存在する必要があります。SQL Server を移動すると、そのデータベースもすべて移動されます。
- Virtual Machines
- Virtual Machines (クラシック) - 「[クラシック デプロイメントの制限事項](#classic-deployment-limitations)」を参照してください
- Virtual Networks

## 移動をサポートしないサービス

現在リソースの移動をサポートしていないサービスは、次のとおりです。

- Application Gateway
- アプリケーション インサイト
- ExpressRoute
- Recovery Services コンテナー - Recovery Services コンテナーに関連付けられているコンピューティング リソース、ネットワーク リソース、ストレージ リソースも移動しないでください。「[Recovery Services の制限事項](#recovery-services-limitations)」を参照してください。
- Virtual Machines スケール セット
- Virtual Networks (クラシック) - 「[クラシック デプロイメントの制限事項](#classic-deployment-limitations)」を参照してください
- VPN Gateway

## App Service の制限事項

App Service アプリを使用している場合、App Service プランのみを移動することはできません。App Service アプリを移動するには、次のオプションがあります。

- App Service プランとそのリソース グループ内の他のすべての App Service リソースを、まだ App Service リソースが含まれていない新しいリソース グループに移動する。この要件により、App Service プランに関連付けられていない App Service リソースも移動する必要があります。
- アプリを別のリソース グループに移動し、元のリソース グループにも App Service プランをすべて保持する。

元のリソース グループに Application Insights のリソースも含まれている場合、Application Insights では現在移行操作がサポートされていないため、このリソースを移動できません。App Service アプリを移動する際に Application Insights のリソースも含めると、移動操作自体が失敗します。ただし、アプリが正常に動作するために、Application Insights と App Service プランがそのアプリと同じリソース グループ内に存在する必要はありません。

たとえば、リソース グループに次のものが含まれているとします。

- **plan-a** および **app-insights-a** に関連付けられた **web-a**
- **plan-b** および **app-insights-b** に関連付けられた **web-b**

オプションは次のとおりです。

- **web-a**、**plan-a**、**web-b**、および **plan-b** を移動する
- **web-a** および **web-b** を移動する
- **web-a** を移動する
- **web-b** を移動する

これ以外のすべての組み合わせでは、移動できないリソースの種類 (Application Insights) の移動、または App Service プランの移動時に残しておくことができないリソースの種類 (すべての種類の App Service リソース) の保持のどちらかが行われます。

Web アプリがその App Service プランとは異なるリソース グループに存在するが、その両方を新しいリソース グループに移動する場合、移動を 2 段階で行う必要があります。For example:

- **web-group** に存在する **web-a**
- **plan-group** に存在する **plan-a**
- **web-a** および **plan-a** を **combined-group** に配置しようとしている

この移動を実行するには、次の順序で 2 つの移動操作を個別に実行します。

1. **web-a** を **plan-group** に移動します
2. **web-a** および **plan-a** を **combined-group** に移動します

## Recovery Services の制限事項

Azure Site Recovery で障害復旧を設定するときに使用されるストレージ リソース、ネットワーク リソース、またはコンピューティング リソースでは移動がサポートされていません。

たとえば、ストレージ アカウント (Storage1) へのオンプレミス コンピューターのレプリケーションが設定済みで、Azure へのフェールオーバー後、保護されたコンピューターを、Azure 仮想ネットワーク (Network1) に接続された仮想マシン (VM1) として使用する必要があるとします。こうした Azure リソース、つまり Storage1、VM1、および Network1 はどれも、同じサブスクリプション内のリソース グループ間、またはサブスクリプション間で移動することはできません。

## クラシック デプロイメントの制限事項

クラシック モデルを使用してデプロイされるリソースを移動するためのオプションは、リソースをサブスクリプション内で移動するか、新しいサブスクリプションに移動するかによって異なります。

**同じサブスクリプション内**のリソース グループ間でリソースを移動する場合は、次の制限が適用されます。

- Virtual Networks (クラシック) を移動することはできません。
- Virtual Machines (クラシック) はクラウド サービスで移動する必要があります。
- クラウド サービスは、移動にその仮想マシンがすべて含まれている場合にのみ移動できます。
- 一度に移動できるクラウド サービスは 1 つだけです。
- 一度に移動できるストレージ アカウント (クラシック) は 1 つだけです。
- ストレージ アカウント (クラシック) は、仮想マシンまたはクラウド サービスと同じ操作では移動できません。

リソースを**新しいサブスクリプション**に移動する場合、次の制限が適用されます。

- サブスクリプション内のすべてのクラシック リソースは、同じ操作で移動する必要があります。
- 対象のサブスクリプションには、他のクラシック リソースは含めないでください。
- クラシック リソースの場合、移動は、別の REST API を通じてのみ要求できます。標準の Resource Manager の移動コマンドは、クラシック リソースを新しいサブスクリプションに移動する場合は機能しません。

クラシック リソースを、**同じサブスクリプション内**の新しリソース グループに移動するには、[ポータル](#use-portal)、[Azure PowerShell](#use-powershell)、[Azure CLI](#use-azure-cli)、または [REST API](#use-rest-api) を使用します。

**クラシック リソースを新しいサブスクリプション**に移動するには、クラシック リソース固有の REST 操作を使用する必要があります。クラシック リソースのサブスクリプション間移動で、サブスクリプションがソース/ターゲット サブスクリプションとして参加できるかどうかを確認するには、次の操作を行います。

    POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    
ソース サブスクリプションの場合、次の要求本文を使用します。

    {
        "role": "source"
    }

ターゲット サブスクリプションの場合、次の要求本文を使用します。

    {
        "role": "target"
    }

どちらの検証操作の応答も、次のようになります。

    {
        "status": "{status}",
        "reasons": [
            "reason1",
            "reason2"
        ]
    }

すべてのクラシック リソースをあるサブスクリプションから別のサブスクリプションに移動するには、次の操作を行います。

    POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01

要求本文:

    {
        "target": "/subscriptions/{target-subscription-id}"
    }


## ポータルの使用

同じサブスクリプション内の新しいリソース グループにリソースを移動するには、そのリソースが含まれるリソース グループを選択し、**[移動]** を選択します。

![リソースの移動](./media/resource-group-move-resources/edit-rg-icon.png)

新しいサブスクリプションにリソースを移動するには、そのリソースが含まれるリソース グループを選択し、サブスクリプションの編集アイコンを選択します。

![リソースの移動](./media/resource-group-move-resources/change-subscription.png)

移動するリソースを、移動先のリソース グループを選択します。そのリソースのスクリプトを更新する必要があること確認し、**[OK]** を選択します。前の手順でサブスクリプションの編集アイコンを選択した場合は、移動先のサブスクリプションも選択する必要があります。

![移動先の選択](./media/resource-group-move-resources/select-destination.png)

**[通知]** に、移動操作が実行されていることが表示されます。

![移動の状態の表示](./media/resource-group-move-resources/show-status.png)

完了すると、結果が表示されます。

![移動の結果の表示](./media/resource-group-move-resources/show-result.png)

## PowerShell の使用

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、**Move-AzureRmResource** コマンドを使用します。

最初の例では、1 つのリソースを新しいリソース グループに移動する方法を示します。

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

2 番目の例では、複数のリソースを新しいリソース グループに移動する方法を示します。

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

新しいサブスクリプションに移動する場合は、**DestinationSubscriptionId** パラメーターの値を含めます。

指定したリソースの移動を確認するように求められます。

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## Azure CLI の使用

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、**azure resource move** コマンドを使用します。次の例は、Redis Cache を新しいリソース グループに移動する方法を示しています。 **-I** パラメーターには、移動するリソース ID のコンマ区切りリストを指定します。

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
	
指定したリソースの移動を確認するように求められたら、
	
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## REST API を使用する

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、次のコマンドを実行します。

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

要求の本文で、ターゲット リソース グループと、移動するリソースを指定します。REST による移動操作の詳細については、「[リソースの移動](https://msdn.microsoft.com/library/azure/mt218710.aspx)」を参照してください。




## 次のステップ
- サブスクリプションを管理するための PowerShell コマンドレットについては、「[Resource Manager での Azure PowerShell の使用](powershell-azure-resource-manager.md)」を参照してください。
- サブスクリプションを管理するための Azure CLI コマンドについては、「[リソース マネージャーでの Azure CLI の使用](xplat-cli-azure-resource-manager.md)」を参照してください。
- サブスクリプションを管理するためのポータル機能については、[Azure Portal を使用したリソースの管理](./azure-portal/resource-group-portal.md)に関するページをご覧ください。
- リソースを論理的に整理する方法については、「[タグを使用したリソースの整理](resource-group-using-tags.md)」を参照してください。

<!---HONumber=AcomDC_0914_2016-->