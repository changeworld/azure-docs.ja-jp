<properties
   pageTitle="リソース マネージャーでサポートされるサービス、リージョン、スキーマ、およびバージョン |Microsoft Azure"
   description="リソース マネージャーをサポートするリソース プロバイダー、そのスキーマと利用可能な API バージョン、およびリソースをホストできるリージョンについて説明します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/22/2015"
   ms.author="tomfitz"/>

# リソース マネージャーのプロバイダー、リージョン、API のバージョン、およびスキーマ

Azure リソース マネージャーは、アプリケーションを構成するサービスをデプロイして管理するための新しい方法を提供します。すべてではありませんがほとんどのサービスはリソース マネージャーをサポートし、一部のサービスはリソース マネージャーを部分的にのみサポートします。Microsoft は将来のソリューションに重要なすべてのサービスに対してリソース マネージャーを有効にする予定ですが、サポートが一貫したものになるまでは、各サービスの現在の状態を把握する必要があります。このトピックでは、Azure リソース マネージャーに対してサポートされるリソース プロバイダーの一覧を示します。

リソースをデプロイするときは、リソースをサポートしているリージョンとリソースで利用できる API バージョンも知っておく必要があります。「[サポートされているリージョン](#supported-regions)」セクションでは、サブスクリプションおよびリソースで有効なリージョンを確認する方法が示されています。「[サポートされる API バージョン](#supported-api-versions)」セクションでは、利用できる API バージョンを決定する方法について紹介します。

Azure ポータルとクラシック ポータルでサポートされているサービスについては、[Azure ポータルで利用できるサービスの表](https://azure.microsoft.com/features/azure-portal/availability/)を参照してください。

次の表では、各サービスがリソース マネージャーによるデプロイメントと管理をサポートするかどうかを示します。「**リソースの移動**」列は、この種類のリソースを新しいリソース グループおよび新しいサブスクリプションの両方に移動できるかどうかを示します。"**クイック スタート テンプレート**" 列のリンクは、指定されたリソース プロバイダーに対するクエリを Azure クイック スタート テンプレート レポジトリに送信します。クイック スタート テンプレートは、頻繁に追加、更新されます。特定のサービスに対するリンクがあっても、必ずしもクエリによってリポジトリからテンプレートが返されるとは限りません。


## コンピューティング

| サービス | リソース マネージャーが有効 | リソースの移動 | REST API | スキーマ | クイック スタート テンプレート |
| ------- | ------------------------ | -------------- |-------- | ------ | ------ |
| Virtual Machines | はい | なし | [VM の作成](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [Microsoft.Compute](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%22&type=Code) |
| Batch | はい | あり | [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) | | [Microsoft.Batch](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Batch%22&type=Code) |
| Dynamics Lifecycle Services | はい | | | | [Microsoft.DynamicsLcs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DynamicsLcs%22&type=Code)
| Service Fabric (プレビュー) | はい | | [Service Fabric Rest](https://msdn.microsoft.com/library/azure/dn707692.aspx) | | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Virtual Machines (クラシック) | 限定的 | 部分的 (後述参照) | - | - |
| Remote App | いいえ | - | - | - |

「Virtual Machines (クラシック)」とは、リソース マネージャー デプロイメント モデルではなくクラシック デプロイメント モデルを使用してデプロイされたリソースのことです。一般に、このようなリソースはリソース マネージャーの操作をサポートしていませんが、有効になっている操作がいくつかあります。これらのデプロイメント モデルの詳細については、「[リソース マネージャー デプロイと従来のデプロイを理解する](resource-manager-deployment-model.md)」を参照してください。

Virtual Machines (クラシック) リソースは、新しいリソース グループには移動できますが、新しいサブスクリプションには移動できません。

## ネットワーク

| サービス | リソース マネージャーが有効 | リソースの移動 | REST API | スキーマ | クイック スタート テンプレート |
| ------- | -------  | -------------- | -------- | ------ | ------ |
| Application Gateway | はい | | | | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS | はい | | [DNS ゾーンの作成](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| Load Balancer | はい | | [Load Balancer の作成](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Virtual Networks | はい | なし | [Create Virtual Network](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Traffic Manager | はい | | [Traffic Manager プロファイルの作成](https://msdn.microsoft.com/library/azure/mt163581.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| ExpressRoute | はい | なし | [ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |

## データ + ストレージ

| サービス | リソース マネージャーが有効 | リソースの移動 | REST API | スキーマ | クイック スタート テンプレート |
| ------- | ------- | -------------- | -------- | ------ | ------- | ------ |
| DocumentDB | はい | あり | [DocumentDB REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Storage | はい | なし | [Storage の作成](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [ストレージ アカウント](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| Redis Cache | はい | あり | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| SQL Database | はい | あり | [データベースの作成](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| Search | はい | あり | [Search REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) | | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| SQL Data Warehouse | はい | | | |
| StorSimple | いいえ | - | - | - | 
| Managed Cache | いいえ | - | - | - |

## Web とモバイル

| サービス | リソース マネージャーが有効 | リソースの移動 | REST API | スキーマ | クイック スタート テンプレート |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| API Management | はい | あり | [Create API](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| API Apps | はい | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) | [API Apps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Web Apps | はい | あり、制限あり (後述参照) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |
| Notification Hubs | はい | あり | [Notification Hub の作成](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |
| Logic Apps | はい | あり | | | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Mobile Engagement | はい | あり | | | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |

Web アプリを使用している場合、App Service プランのみを移動することはできません。Web アプリを移動するには、次のオプションがあります。

- 移動先のリソース グループにまだ Microsoft.Web リソースがない場合は、すべてのリソースをあるリソース グループから別のリソース グループに移動します。
- Web アプリは別のリソース グループに移動しますが、App Service プランは元のリソース グループで保持します。

## 分析

| サービス | リソース マネージャーが有効 | リソースの移動 | REST API | スキーマ | クイック スタート テンプレート |
| ------- | -------  | -------------- | -------- | ------ | ------ |
| イベント ハブ | はい | | [Event Hub の作成](https://msdn.microsoft.com/library/azure/dn790676.aspx) | | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| Stream Analytics | はい | | [Steam Analytics REST](https://msdn.microsoft.com/library/azure/dn835031.aspx) | | [Microsoft.StreamAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.StreamAnalytics%22&type=Code) |
| HDInsights | はい | あり | [クラスターの作成](https://msdn.microsoft.com/library/azure/mt622302.aspx) | | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Data Factory | はい | あり | [Data Factory の作成](https://msdn.microsoft.com/library/azure/dn906717.aspx) | | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Machine Learning | いいえ | - | - | - | 
| Data Catalog | いいえ | - | - | - |

## メディアと CDN

| サービス | リソース マネージャーが有効 | リソースの移動 | REST API | スキーマ | クイック スタート テンプレート |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| CDN | はい | | [CDN REST](https://msdn.microsoft.com/library/azure/mt634456.aspx) | | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| メディア サービス | いいえ | | | |


## ハイブリッド統合

| サービス | リソース マネージャーが有効 | リソースの移動 | REST API | スキーマ | クイック スタート テンプレート |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| BizTalk Services | はい | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | [Microsoft.BizTalkServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BizTalkServices%22&type=Code) |
| Service Bus | はい | | [Service Bus REST](https://msdn.microsoft.com/library/azure/hh780717.aspx) | | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |
| Backup | いいえ | - | - | - |
| Site Recovery | いいえ | - | - | - |

## ID 管理とアクセス管理 

| サービス | リソース マネージャーが有効 | リソースの移動 | REST API | スキーマ | クイック スタート テンプレート |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Azure Active Directory | いいえ | - | - | - |
| Azure Actice Directory B2C | いいえ | - | - | - |
| Multi-Factor Authentication | いいえ | - | - | - |

## 開発者サービス 

| サービス | リソース マネージャーが有効 | リソースの移動 | REST API | スキーマ | クイック スタート テンプレート |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Application Insights | はい | なし | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing Maps | はい | | | | [Microsoft.BingMaps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BingMaps%22&type=Code) |
| DevTest Labs (プレビュー) | はい | | | [2015-05-21-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-05-21-preview/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Visual Studio アカウント | はい | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | [Microsoft.VisualStudio](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.VisualStudio%22&type=Code) |

## 管理とセキュリティ

| サービス | リソース マネージャーが有効 | リソースの移動 | REST API | スキーマ | クイック スタート テンプレート |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Automation | はい | あり | | | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Key Vault | はい | あり | [Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Key Vault](resource-manager-template-keyvault.md)<br />[Key Vault シークレット](resource-manager-template-keyvault-secret.md) | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Scheduler | はい | | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Operational Insights | はい | あり | | | [Microsoft.OperationalInsights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.OperationalInsights%22&type=Code) |
| IoTHubs | はい | | [IoT Hub の作成](https://msdn.microsoft.com/library/azure/mt589013.aspx) | | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| セキュリティ (プレビュー) | はい | | | | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## リソース マネージャー

| 機能 | リソース マネージャーが有効 | リソースの移動 | REST API | スキーマ | クイック スタート テンプレート |
| ------- | ------- | -------- | -------------- | -------- | ------ | ------ |
| 承認 | はい | 該当なし | [管理ロック](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[ロール ベースのアクセス制御](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [リソース ロック](resource-manager-template-lock.md)<br />[ロールの割り当て](resource-manager-template-role.md) | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| リソース | はい | 該当なし | [リンク済みリソース](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [リソース リンク](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## サポートされているリージョン

通常、リソースをデプロイするときはリソースのリージョンを指定する必要があります。リソース マネージャーはすべてのリージョンでサポートされていますが、デプロイするリソースはすべてのリージョンではサポートされていない場合があります。さらに、サブスクリプションでの制限により、リソースをサポートする一部のリージョンを使用できない場合があります。これらの制限事項は、本国での税金に関する問題のため、またはサブスクリプション管理者によって設けられた、使用を特定のリージョンに制限するポリシーの結果である場合があります。

全 Azure サービスを対象とする全サポート リージョンの完全な一覧が必要な場合は、「[リージョン別のサービス](https://azure.microsoft.com/regions/#services)」を参照してください。ただし、一覧には、ご利用のサブスクリプションではサポートされていないリージョンが含まれている可能性があります。次のコマンドを実行すると、ご利用のサブスクリプションでサポートされているリージョンをリソース タイプ別に確認できます。

### REST API

ご利用のサブスクリプションの特定のリソースの種類で使用可能なリージョンを確認するには、[すべてのリソース プロバイダーの一覧表示](https://msdn.microsoft.com/library/azure/dn790524.aspx)操作を利用します。

### PowerShell

次の例では、Azure PowerShell 1.0 を使用して Web サイトのサポートされるリージョンを取得する方法を示します。1.0 リリースの詳細については、「[Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)」を参照してください。

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
次のように出力されます。

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

Azure PowerShell 0.9.8 では、次のコマンドを使用します。

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

### Azure CLI

次の例では、リソースの種類ごとにサポートされるすべての場所が返されます。

    azure location list

また、**jq** などのツールを使用して場所の結果をフィルターすることもできます。jq などのツールについて学習するには、「[Azure とやり取りする便利なツール](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure)」を参照してください。

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

次のような結果が返されます。

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## サポートされている API バージョン

テンプレートをデプロイするとき、各リソースの作成に使用する API バージョンを指定する必要があります。API バージョンは、リリース プロバイダーがリリースする REST API のバージョンに一致します。リソース プロバイダーは新しい機能を有効にするとき、REST API の新しいバージョンをリリースします。そのため、テンプレートで指定した API のバージョンにより、テンプレートで指定できるプロパティが変わります。一般的に、新しいテンプレートを作成するとき、最新の API バージョンを選択します。既存のテンプレートの場合、以前の API バージョンの使用を続けるか、テンプレートを最新版に更新して新しい機能を最大限に活用するか決定できます。

### REST API

リソースの種類で使用可能な API バージョンを確認するには、[すべてのリソース プロバイダーの一覧表示](https://msdn.microsoft.com/library/azure/dn790524.aspx)操作を利用します。

### PowerShell

次の例では、Azure PowerShell 1.0 を利用し、特定のリソースの種類で利用できる API バージョンを取得する方法を示します。

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
次のように出力されます。
    
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

Azure PowerShell 0.9.8 では、次を使用します。

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Azure CLI

次のコマンドで、リソース プロバイダーの情報 (利用可能な API バージョンを含む) をファイルに保存できます。

    azure provider show Microsoft.Web -vv --json > c:\temp.json

ファイルを開き、**apiVersions** 要素を検索できます。

## 次のステップ

- リソース マネージャーのテンプレートの作成の詳細については、[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
- リソースをデプロイする方法を確認するには、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。

<!----HONumber=AcomDC_1223_2015-->