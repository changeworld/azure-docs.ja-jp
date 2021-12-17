---
title: Azure プライベート エンドポイントとは
description: Azure プライベート エンドポイントについて学習します
services: private-link
author: asudbring
ms.service: private-link
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: allensu
ms.openlocfilehash: 485cd0d3d7ce2c64bfb0f37e07d785dee8808e3a
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062673"
---
# <a name="what-is-azure-private-endpoint"></a>Azure プライベート エンドポイントとは

プライベート エンドポイントは、仮想ネットワークのプライベート IP アドレスを使用するネットワーク インターフェイスです。 ユーザーはこのネットワーク インターフェイスにより、Azure Private Link を利用するサービスにプライベートかつ安全に接続します。 プライベート エンドポイントを有効にして、サービスを仮想ネットワークに取り込みます。

サービスには、以下のような Azure サービスが利用できます。

* Azure Storage
* Azure Cosmos DB
* Azure SQL データベース
* [Private Link サービス](private-link-service-overview.md)を使用する独自のサービス
  
## <a name="private-endpoint-properties"></a>プライベート エンドポイントのプロパティ 
 プライベート エンドポイントでは、次のプロパティを指定します。 

|プロパティ  |説明 |
|---------|---------|
|名前    |    リソース グループ内の一意の名前。      |
|Subnet    |  デプロイするサブネットと、プライベート IP アドレスが割り当てられている場所。 サブネットの要件については、この記事の制限事項に関するセクションを参照してください。         |
|Private Link リソース    |   使用可能な種類の一覧から、リソース ID または別名を使用して接続するプライベート リンク リソース。 このリソースに送信されるすべてのトラフィックに対して、一意のネットワーク識別子が生成されます。       |
|ターゲット サブリソース   |      接続するサブリソース。 それぞれの種類のプライベート リンク リソースには、好みに応じて選択できるさまざまなオプションがあります。    |
|接続の承認方法    |  自動または手動。 Azure ロールベースのアクセス制御のアクセス許可によっては、自分のプライベート エンドポイントを自動的に承認することができます。 Azure ロールベースのアクセス制御を使用せずにプライベート リンク リソースに接続する場合は、手動による方法を使用して、リソースの所有者が接続を承認できるようにします。        |
|要求メッセージ     |  要求された接続を手動で承認するためのメッセージを指定できます。 このメッセージは、特定の要求を識別するために使用できます。        |
|[接続状態]   |   プライベート エンドポイントがアクティブかどうかを指定する読み取り専用のプロパティ。 トラフィックの送信に使用できるのは、承認済み状態のプライベート エンドポイントのみです。 使用可能なその他の状態: <br>- **[Approved]\(承認済み\)** : 接続が自動または手動で承認され、使用する準備が整っています。</br><br>- **[Pending]\(保留中\)** : 接続が手動で作成されており、プライベート リンク リソースの所有者による承認を待っています。</br><br>- **[Rejected]\(拒否済み\)** : プライベート リンク リソースの所有者によって接続が拒否されました。</br><br>- **[Disconnected]\(切断済み\)** : プライベート リンク リソースの所有者によって接続が削除されました。 プライベート エンドポイントは情報が多くなり、クリーンアップのために削除する必要があります。 </br>|

プライベート エンドポイントに関する重要な詳細情報の一部を示します。 

- プライベート エンドポイントを使用すると、同一の以下のものに属するコンシューマー間で接続できるようになります。
    
    - Virtual Network
    - リージョンでピアリングされた仮想ネットワーク
    - グローバルにピアリングされた仮想ネットワーク
    - [VPN](https://azure.microsoft.com/services/vpn-gateway/) または [Express Route](https://azure.microsoft.com/services/expressroute/) を使用するオンプレミス
    - Private Link を利用するサービス
 
- ネットワーク接続は、プライベート エンドポイントに接続するクライアントによってのみ開始できます。 サービス プロバイダーは、サービス コンシューマーへの接続を作成するルーティング構成を持ちません。 接続は一方向にのみ確立できます。

- プライベート エンドポイントを作成すると、リソースのライフサイクルにわたって読み取り専用のネットワーク インターフェイスが作成されます。 このインターフェイスには、プライベート リンク リソースにマップされる、サブネットからの動的プライベート IP アドレスが割り当てられます。 プライベート IP アドレスの値は、プライベート エンドポイントのライフサイクル全体にわたって変更されません。
 
- プライベート エンドポイントは、仮想ネットワークと同じリージョンとサブスクリプションにデプロイする必要があります。 
 
- プライベート リンク リソースは、仮想ネットワークおよびプライベート エンドポイントとは別のリージョンにデプロイできます。
 
- 同じプライベート リンク リソースを使用して、複数のプライベート エンドポイントを作成できます。 共通の DNS サーバー構成を使用する単一のネットワークでは、特定のプライベート リンク リソースに単一のプライベート エンドポイントを使用することが推奨される方法です。 この方法を使用して、DNS の解決で重複するエントリや競合を回避します。 
 
- 同じ仮想ネットワーク内の同じサブネットまたは異なるサブネットに複数のプライベート エンドポイントを作成できます。 サブスクリプションに作成できるプライベート エンドポイントの数には制限があります。 詳細については、 [Azure の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)に関する記事を参照してください。

- プライベート リンク リソースからのサブスクリプションも、Micosoft. Network リソース プロバイダーに登録されている必要があります。 詳細については、 [Azure リソース プロバイダー](../azure-resource-manager/management/resource-providers-and-types.md)に関する記事を参照してください。
 
## <a name="private-link-resource"></a>プライベート リンク リソース 
プライベート リンク リソースは、特定のプライベート エンドポイントの宛先ターゲットです。 

下の表に、プライベート エンドポイントをサポートする、使用可能なリソースを一覧で示します。 
 
| プライベート リンク リソース名 | リソースの種類 | サブリソース |
| ---------------------------| ------------- | ------------- |
| **Azure App Configuration** | Microsoft.Appconfiguration/configurationStores | configurationStores |
| **Azure Automation** | Microsoft.Automation/automationAccounts | Webhook、DSCAndHybridWorker |
| **Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts | Sql、MongoDB、Cassandra、Gremlin、Table |
| **Azure Batch** | Microsoft.Batch/batchAccounts | Batch アカウント |
| **Azure Cache for Redis** | Microsoft.Cache/Redis | redisCache |
| **Azure Cache for Redis Enterprise** | Microsoft.Cache/redisEnterprise | redisEnterprise |
| **Cognitive Services** | Microsoft.CognitiveServices/accounts | account |
| **Azure Managed Disks** | Microsoft.Compute/diskAccesses | managed disk |
| **Azure Container Registry** | Microsoft.ContainerRegistry/registries | 使用) |
| **Azure Kubernetes Service - Kubernetes API** | Microsoft.ContainerService/managedClusters | management |
| **Azure Data Factory** | Microsoft.DataFactory/factories | Data Factory |
| **Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers | mariadbServer |
| **Azure Database for MySQL** | Microsoft.DBforMySQL/servers | mysqlServer |
| **Azure Database for PostgreSQL - 単一サーバー** | Microsoft.DBforPostgreSQL/servers | postgresqlServer |
| **Azure IoT Hub** | Microsoft.Devices/IotHubs | iotHub |
| **Microsoft Digital Twins** | Microsoft.DigitalTwins/digitalTwinsInstances | digitaltwinsinstance |
| **Azure Event Grid** | Microsoft.EventGrid/domains | domain |
| **Azure Event Grid** | Microsoft.EventGrid/topics  | Event Grid トピック |
| **Azure Event Hub** | Microsoft.EventHub/namespaces | namespace |
| **Azure HDInsight** | Microsoft.HDInsight/clusters | cluster |
| **Azure API for FHIR** | Microsoft.HealthcareApis/services | service |
| **Azure Keyvault HSM** | Microsoft.Keyvault/managedHSMs | HSM (HSM) |
| **Azure Key Vault** | Microsoft.KeyVault/vaults | コンテナー |
| **Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces | amlworkspace |
| **Azure Migrate** | Microsoft.Migrate/assessmentProjects | project |
| **Application Gateway** | Microsoft.Network/applicationgateways | application gateway |
| **Private Link サービス** (独自のサービス) |  Microsoft.Network/privateLinkServices | empty |
| **Power BI** | Microsoft.PowerBI/privateLinkServicesForPowerBI | Power BI |
| **Azure Purview** | Microsoft.Purview/accounts | account |
| **Azure Purview** | Microsoft.Purview/accounts | ポータル |
| **Azure Backup** | Microsoft.RecoveryServices/vaults | コンテナー |
| **Azure Relay** | Microsoft.Relay/namespaces | namespace |
| **Microsoft Search** | Microsoft.Search/searchServices | search service |
| **Azure Service Bus** | Microsoft.ServiceBus/namespaces | namespace |
| **SignalR** | Microsoft.SignalRService/SignalR | signalr |
| **SignalR** | Microsoft.SignalRService/webPubSub | webpubsub |
| **Azure SQL Database** | Microsoft.Sql/servers | SQL Server (sqlServer) |
| **Azure ストレージ** | Microsoft.Storage/storageAccounts | BLOB (blob、blob_secondary)<BR> Table (table、table_secondary)<BR> Queue (queue、queue_secondary)<BR> File (file、file_secondary)<BR> Web (web、web_secondary) |
| **Azure File Sync** | microsoft.storagesync/storagesyncservices | File Sync Service |
| **Azure Synapse** | Microsoft.Synapse/privateLinkHubs | synapse |
| **Azure Synapse Analytics** | Microsoft.Synapse ワークスペース | Sql, SqlOnDemand, Dev | 
| **Azure App Service** | Microsoft.Web/hostingEnvironments | hosting environment |
| **Azure App Service** | Microsoft.Web/sites | sites |
| **Azure App Service** | Microsoft.Web/staticSites | staticSite |

## <a name="network-security-of-private-endpoints"></a>プライベート エンドポイントのネットワーク セキュリティ 

プライベート エンドポイントの使用時にトラフィックがセキュリティで保護されるのは、プライベート リンク リソースに対してです。 プラットフォームによってアクセス制御が実行されて、ネットワーク接続が、指定したプライベート リンク リソースにのみ到達していることが検証されます。 同じ Azure サービス内の他のリソースにアクセスするには、追加のプライベート エンドポイントが必要です。 
 
パブリック エンドポイントにアクセスしてサポートされている Azure サービスに接続できないように、対象のワークロードを完全にロックダウンすることができます。 この制御によって、リソースに追加のネットワーク セキュリティ層が提供されます。 このセキュリティにより、同じ Azure サービスでホストされている他のリソースへのアクセスを防止する保護が提供されます。 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>承認ワークフローを使用したプライベート リンク リソースへのアクセス 
次の接続の承認方法を使用して、プライベート リンク リソースに接続できます。
- **自動的** な承認: 特定のプライベート リンク リソースに対するアクセス許可を自ら所有しているか、付与されている場合。 必要なアクセス許可は、次の形式のプライベート リンク リソースの種類に基づいて行います: Microsoft.\<Provider>/<resource_type>/privateEndpointConnectionsApproval/action
- **手動** による要求: 必要な許可がなく、アクセスを要求したい場合。 承認ワークフローが開始されます。 プライベート エンドポイントとそれ以降のプライベート エンドポイント接続は、"Pending (保留中)" 状態で作成されます。 プライベート リンク リソースの所有者は、接続を承認する役割を担います。 次の承認ワークフロー ダイアグラムに示すように、プライベート エンドポイントは、承認されると、トラフィックを正常に送信できるようになります。  

![ワークフローの承認](media/private-endpoint-overview/private-link-paas-workflow.png)
 
プライベート リンク リソースの所有者は、プライベート エンドポイント接続で次のアクションを実行できます。 

- すべてのプライベート エンドポイント接続の詳細を確認する。 
- プライベート エンドポイント接続を承認する。 対応するプライベート エンドポイントは、プライベート リンク リソースにトラフィックを送信できるようになります。 
- プライベート エンドポイント接続を拒否する。 対応するプライベート エンドポイントが更新され、状態が反映されます。
- 任意の状態のプライベート エンドポイント接続を削除する。 対応するプライベート エンドポイントは、このアクションを反映して切断済み状態に更新されます。プライベート エンドポイントの所有者は、この時点でのみリソースを削除できます。 
 
> [!NOTE]
> 承認済み状態のプライベート エンドポイントのみが、指定されたプライベート リンク リソースにトラフィックを送信できます。 

### <a name="connect-with-alias"></a>別名を使用して接続する

別名は、サービスの所有者が標準のロード バランサーの背後にプライベート リンク サービスを作成したときに生成される一意のモニカーです。 サービスの所有者は、この別名をオフラインでコンシューマーと共有できます。 

コンシューマーは、リソース URI または別名のいずれかを使用して、プライベート リンク サービスへの接続を要求できます。 別名を使用して接続する場合は、手動の接続承認方法を使用してプライベート エンドポイントを作成する必要があります。 手動の接続承認方法を使用するには、プライベート エンドポイント作成フロー中に手動要求パラメーターを true に設定します。 詳細については、「[New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)」と「[az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create)」を参照してください。

## <a name="dns-configuration"></a>DNS の構成

プライベート リンク リソースへの接続のために使用される DNS 設定は重要です。 接続のために完全修飾ドメイン名 (FQDN) を使用する場合は、DNS 設定が正しいことを確認してください。 設定は、プライベート エンドポイントのプライベート IP アドレスに解決される必要があります。 既存の Azure サービスには、パブリック エンドポイント経由で接続するときに使用する DNS 構成が既に存在している場合があります。 プライベート エンドポイントを使用して接続するには、この構成を上書きする必要があります。 
 
プライベート エンドポイントに関連付けられているネットワーク インターフェイスには、DNS を構成するために必要な情報が含まれています。 この情報には、プライベート リンク リソースの FQDN とプライベート IP アドレスが含まれています。 

プライベート エンドポイントの DNS を構成する際の推奨事項に関するすべての詳細情報については、[プライベート エンドポイントの DNS 構成](private-endpoint-dns.md)に関するページを参照してください。
 
## <a name="limitations"></a>制限事項
 
次の表に、プライベート エンドポイントを使用する場合の既知の制限事項を一覧で示します。 

| 制限事項 | 説明 |対応策 |
| --------- | --------- | --------- |
| ユーザー定義ルートを使用するプライベート エンドポイント宛てのトラフィックは、非対称である可能性があります。 | プライベート エンドポイントから返されるトラフィックは、ネットワーク仮想アプライアンス (NVA) をバイパスして、ソース VM に戻ろうとします。 | 対称的なルーティングが確実に行われるように、ソース ネットワーク アドレス変換 (SNAT) が使用されます。 UDR を使用するプライベート エンドポイント宛てのすべてのトラフィックについては、NVA で、トラフィックのために SNAT を使用することをお勧めします。 |

> [!IMPORTANT]
> プライベート エンドポイントに対する NSG と UDR のサポートは、一部のリージョンでパブリック プレビュー段階です。 詳細については、「[Private Link UDR のサポートのパブリック プレビュー](https://azure.microsoft.com/updates/public-preview-of-private-link-udr-support/)」と「[Private Link ネットワーク セキュリティ グループのサポートのパブリック プレビュー](https://azure.microsoft.com/updates/public-preview-of-private-link-network-security-group-support/)」を参照してください。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="public-preview-limitations"></a>パブリック プレビューの制限事項

### <a name="nsg"></a>NSG

| 制限事項 | 説明 | 対応策 |
| ---------- | ----------- | ---------- |
| プライベート エンドポイント ネットワーク インターフェイスでは "有効なルートとセキュリティ規則を取得する" を使用できない。 | ネットワーク インターフェイスに移動して、有効なルートとセキュリティ規則についての関連情報を表示することができません。 | Q4CY21 |
| NSG フロー ログがサポートされない。 | NSG フロー ログは、プライベート エンドポイント宛ての受信トラフィックに対しては機能しません。 | 現時点では情報がありません。 |
| ZRS ストレージ アカウントの使用時に間欠的切断が発生する。 | ZRS ストレージ アカウントを使用している場合、ストレージのプライベート エンドポイント サブネットへの NSG の適用が許可されていても定期的に間欠的切断が発生することがあります。 | September |
| Azure Key Vault の使用時に間欠的切断が発生する。 | Azure Key Vault を使用している場合、Azure Key Vault のプライベート エンドポイント サブネットへの NSG の適用が許可されていても定期的に間欠的切断が発生することがあります。 | September |
| NSG あたりのアドレス プレフィックスの数に制限がある。 | 1 つの規則で 500 を超える NSG のアドレス プレフィックスを使用することはサポートされていません。 | September |
| AllowVirtualNetworkAccess フラグ | 別の VNet (VNet B) へのピアリング リンクで **AllowVirtualNetworkAccess** フラグが false に設定されている VNet (VNet A) で VNet ピアリングを設定している場合は、**VirtualNetwork** タグを使用して、プライベート エンドポイント リソースにアクセスする VNet B からのトラフィックを拒否することはできません。 プライベート エンドポイントへのトラフィックを拒否するには、VNet B のアドレス プレフィックスのブロックを明示的に配置する必要があります。 | September |
| デュアル ポート NSG ルールがサポートされない。 | NSG 規則で複数のポート範囲が使用されている場合、許可規則と拒否規則に対して、最初のポート範囲だけが有効になります。 複数のポート範囲がある規則では、既定で、特定のポートではなくすべてが拒否されます。 </br> **詳細については、下の規則の例を参照してください。** | September |

| Priority | 送信元ポート | 宛先ポート | アクション | 有効なアクション |
| -------- | ----------- | ---------------- | ------ | ---------------- |
| 10 | 10 ～ 12 | 10 ～ 12 | 許可/拒否 | 送信元/宛先ポートの単一ポート範囲は、想定どおりに動作します。 |
| 10 | 10 ～ 12、13 ～ 14 | 14 ～ 15、16 ～ 17 | Allow | 送信元ポート 10 ～ 12 と宛先ポート 14 ～ 15 のみが許可されます。 |
| 10 | 10 ～ 12、13 ～ 14 | 120 ～ 130、140 ～ 150 | 拒否 | 送信元と宛先のポート範囲が複数存在するため、すべての宛先ポートに対して、すべての送信元ポートからのトラフィックが拒否されます。 |
| 10 | 10 ～ 12、13 ～ 14 | 120 ～ 130 | 拒否 | 宛先ポート 120 ～ 130 のみに対して、すべての発信元ポートからのトラフィックが拒否されます。 複数の送信元ポート範囲と、1 つの宛先ポート範囲があります。 |

**表: 二重ポート規則の例。**

### <a name="udr"></a>UDR

| 制限事項 | 説明 | 対応策 |
| ---------- | ----------- | ---------- |
| ソース ネットワーク アドレス変換 (SNAT) が常に推奨される。 | プライベート エンドポイントのデータプレーンは本来可変的なものであるため、戻りトラフィックが確実に処理されるように、プライベート エンドポイント宛てのトラフィックには SNAT を適用することをお勧めします。 | 現時点では情報がありません。 |
 
## <a name="next-steps"></a>次のステップ

- プライベート エンドポイントとプライベート リンクの詳細については、「[Azure Private Link とは](private-link-overview.md)」を参照してください。
- Web アプリ用のプライベート エンドポイントの作成を開始するには、「[クイックスタート - Azure portal を使用してプライベート エンドポイントを作成する](create-private-endpoint-portal.md)」を参照してください。
