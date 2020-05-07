---
title: Azure プライベート エンドポイントの DNS 構成
description: Azure プライベート エンドポイントの DNS 構成について学習する
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 477a5ffa971120d1a98c09ac4ae8ebda1c82b770
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209028"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Azure プライベート エンドポイントの DNS 構成


接続文字列の一部として完全修飾ドメイン名 (FQDN) を使用してプライベート リンク リソースに接続する場合は、割り当てられたプライベート IP アドレスに解決されるように DNS 設定を正しく構成することが重要です。 既存の Azure サービスには、パブリック エンドポイント経由で接続するときに使用する DNS 構成が既に存在している場合があります。 プライベート エンドポイントを使用して接続するには、これをオーバーライドする必要があります。 
 
プライベート エンドポイントに関連付けられたネットワーク インターフェイスには、特定のプライベート リンク リソースに割り当てられた FQDN やプライベート IP アドレスなど、DNS を構成するために必要な情報の完全なセットが含まれています。 
 
次のオプションを使用して、プライベート エンドポイントの DNS 設定を構成できます。 
- **ホスト ファイルを使用する (テストにのみ推奨)** 。 仮想マシン上のホスト ファイルを使用して、DNS をオーバーライドすることができます。  
- **プライベート DNS ゾーンを使用する**。 [プライベート DNS ゾーン](../dns/private-dns-privatednszone.md)を使用して、特定のプライベート エンドポイントの DNS 解決をオーバーライドすることができます。 プライベート DNS ゾーンを自分の仮想ネットワークにリンクして、特定のドメインを解決することができます。
- **カスタム DNS サーバーを使用する**。 独自の DNS サーバーを使用して、特定のプライベート リンク リソースの DNS 解決をオーバーライドすることができます。 お使いの [DNS サーバー](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)が仮想ネットワーク上にホストされている場合は、プライベート DNS ゾーンを使用する DNS 転送規則を作成して、すべてのプライベート リンク リソースの構成を簡略化することができます。
 
> [!IMPORTANT]
> パブリック エンドポイントを解決する目的でアクティブに使用されているゾーンをオーバーライドすることはお勧めしません。 リソースへの接続は、パブリック DNS への DNS 転送なしでは正しく解決できません。 問題を回避するには、別のドメイン名を作成するか、次の各サービスの推奨される名前に従ってください。 

## <a name="azure-services-dns-zone-configuration"></a>Azure サービス DNS ゾーンの構成
提案されたプライベート ドメイン名に解決をリダイレクトするために、Azure サービスによってパブリック DNS に正規名の DNS レコード (CNAME) が作成されます。 この解決は、プライベート エンドポイントのプライベート IP アドレスでオーバーライドすることができます。 
 
アプリケーションで接続 URL を変更する必要はありません。 パブリック DNS を使用して解決を試みると、DNS サーバーはプライベート エンドポイントに解決するようになります。 このプロセスは既存のアプリケーションに影響しません。 

Azure サービスについては、次の表に示すように、推奨されるゾーン名を使用します。

|Private Link リソースの種類   |サブリソース  |ゾーン名  |
|---------|---------|---------|
|SQL DB (Microsoft.Sql/servers)    |  SQL Server (sqlServer)        |   privatelink.database.windows.net       |
|Azure Synapse Analytics (Microsoft.Sql/servers)    |  SQL Server (sqlServer)        | privatelink.database.windows.net |
|Storage アカウント (Microsoft.Storage/storageAccounts)    |  BLOB (blob、blob_secondary)        |    privatelink.blob.core.windows.net      |
|Storage アカウント (Microsoft.Storage/storageAccounts)    |    Table (table、table_secondary)      |   privatelink.table.core.windows.net       |
|Storage アカウント (Microsoft.Storage/storageAccounts)    |    Queue (queue、queue_secondary)     |   privatelink.queue.core.windows.net       |
|Storage アカウント (Microsoft.Storage/storageAccounts)   |    File (file、file_secondary)      |    privatelink.file.core.windows.net      |
|Storage アカウント (Microsoft.Storage/storageAccounts)     |  Web (web、web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake ファイル システム Gen2 (Microsoft.Storage/storageAccounts)  |  Data Lake ファイル システム Gen2 (dfs、dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|SQL    |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|MongoDB    |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Gremlin    |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|テーブル|privatelink.table.cosmos.azure.com|
|Azure Database for PostgreSQL - シングルサーバー (Microsoft DBforPostgreSQL/servers)|postgresqlServer|privatelink.postgres.database.azure.com|
|Azure Database for MySQL (Microsoft.DBforMySQL/servers)|mysqlServer|privatelink.mysql.database.azure.com|
|Azure Database for MariaDB (Microsoft.DBforMariaDB/servers)|mariadbServer|privatelink.mariadb.database.azure.com|
|Azure Key Vault (Microsoft.KeyVault/vaults)|コンテナー|privatelink.vaultcore.azure.net|
|Azure Kubernetes Service - Kubernetes API (Microsoft.ContainerService/managedClusters)    | managedCluster | {guid}.privatelink.{region}.azmk8s.io|
|Azure Search (Microsoft.Search/searchServices)|searchService|privatelink.search.windows.net|   
|Azure Container Registry (Microsoft.ContainerRegistry/registries) | 使用) | privatelink.azurecr.io |
|Azure App Configuration (Microsoft.Appconfiguration/configurationStores)| configurationStore | privatelink.azconfig.io|
|Azure Backup (Microsoft.RecoveryServices/vaults)| コンテナー |privatelink.{region}.backup.windowsazure.com|
|Azure Event Hub (Microsoft.EventHub/namespaces)| namespace |privatelink.servicebus.windows.net|
|Azure Service Bus (Microsoft.ServiceBus/namespaces) | namespace |privatelink.servicebus.windows.net|
|Azure Relay (Microsoft.Relay/namespaces) | namespace |privatelink.servicebus.windows.net|
|Azure Event Grid (Microsoft.EventGrid/topics)     | topic | topic.{region}.privatelink.eventgrid.azure.net|
|Azure Event Grid (Microsoft.EventGrid/domains) | domain | domain.{region}.privatelink.eventgrid.azure.net |
|Azure WebApps (Microsoft.Web/sites)    | site | privatelink.azurewebsites.net |
|Azure Machine Learning (Microsoft.MachineLearningServices/workspaces)    | ワークスペース | privatelink.api.azureml.ms |
 


## <a name="dns-configuration-scenarios"></a>DNS の構成シナリオ

サービスの FQDN によってパブリックによってパブリック IP アドレスが解決されます。プライベート エンドポイントのプライベート IP アドレスを解決するように DNS 構成を変更する必要があります。

DNS は、プライベート エンドポイント IP アドレスを正しく解決することでアプリケーションを正しく動かすために不可欠なコンポーネントです。

ユーザー設定に基づき、DNS 解決の統合では次のシナリオを利用できます。

- [カスタム DNS サーバーのない仮想ネットワーク ワークロード](#virtual-network-workloads-without-custom-dns-server)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>カスタム DNS サーバーのない仮想ネットワーク ワークロード

この構成は、カスタム DNS サーバーのない仮想ネットワーク ワークロードに適しています。 このシナリオでは、クライアントでは、Azure から提供される DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) にプライベート エンドポイント IP アドレスを問い合わせます。 Azure DNS はプライベート DNS ゾーンの DNS 解決を担当します。


 > [!NOTE]
> このシナリオでは、Azure SQL データベースから推奨されるプライベート DNS ゾーンが使用されます。 その他のサービスの場合、[Azure サービス DNS ゾーンの構成](#azure-services-dns-zone-configuration)を参照してモデルを調整できます。

適切に構成するには、次のリソースが必要です。

- クライアント仮想ネットワーク

- プライベート DNS ゾーン [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) と[タイプ A レコード](../dns/dns-zones-records.md#record-types)

- プライベート エンドポイント情報 (FQDN レコード名とプライベート IP アドレス)

次の図には、プライベート DNS ゾーンを利用した仮想ネットワーク ワークロードからの DNS 解決シーケンスが示されています。

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="単一の仮想ネットワークと Azure 提供の DNS":::

このモデルは、同じプライベート エンドポイントに関連付けられている複数のピアリングされた仮想ネットワークに拡張できます。 これは、ピアリングされたすべての仮想ネットワークに対してプライベート DNS ゾーンに[新しい仮想ネットワーク リンクを追加する](../dns/private-dns-virtual-network-links.md)ことで行うことができます。

 > [!IMPORTANT]
>  この構成には単一の DNS ゾーンが必要です。異なる仮想ネットワークに対して同じ名前で複数のゾーンを作成する場合、手作業で DNS レコードを結合しなければならないことがあります。

このシナリオには、[ハブとスポーク](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)のネットワーク トポロジがあります。スポーク ネットワークで共通プライベート エンドポイントを共有し、すべてのスポーク仮想ネットワークが同じプライベート DNS ゾーンにリンクされています。 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="ハブとスポーク。DNS が Azure から提供されています":::


## <a name="next-steps"></a>次のステップ
- [プライベート エンドポイントについて学習します](private-endpoint-overview.md)
