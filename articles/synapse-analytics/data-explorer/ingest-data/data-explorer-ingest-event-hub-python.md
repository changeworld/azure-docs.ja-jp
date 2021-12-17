---
title: Python インジェストを使用してイベント ハブから Azure Synapse Data Explorer にデータを取り込む (プレビュー)
description: Python を使用してイベント ハブから Azure Synapse Data Explorer にデータを取り込む (読み込む) 方法について学習します。
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 64327836a5b2b9559699c4516cfaf4710b2754bb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477966"
---
# <a name="create-an-event-hub-data-connection-for-azure-synapse-data-explorer-by-using-python-preview"></a>Python を使用して Azure Synapse Data Explorer 用にイベント ハブ データ接続を作成する (プレビュー)

> [!div class="op_single_selector"]
> * [ポータル](data-explorer-ingest-event-hub-portal.md)
> * [ワンクリック](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Azure Resource Manager テンプレート](data-explorer-ingest-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

この記事では、Python を使用して Azure Synapse Data Explorer 用にイベント ハブ データ接続を作成します。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- [取り込み用のデータが含まれるイベント ハブ](data-explorer-ingest-event-hub-portal.md#create-an-event-hub)。
- [Python 3.4 以上](https://www.python.org/downloads/)。
<!-- - [Database and table policies](database-table-policies-python.md) (optional). -->

[!INCLUDE [data-explorer-ingest-event-hub-table-mapping](../includes/data-explorer-ingest-event-hub-table-mapping.md)]

[!INCLUDE [data-explorer-data-connection-install-package-python](../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>イベント ハブ データ接続を追加する

次の例は、イベント ハブ データ接続をプログラムを使用して追加する方法を示しています。 Azure portal を使用してイベント ハブ データ接続を追加する方法については、「[イベント ハブへの接続](data-explorer-ingest-event-hub-portal.md#connect-to-the-event-hub)」を参照してください。

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**設定** | **推奨値** | **フィールドの説明**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | テナント ID。 ディレクトリ ID とも呼ばれます。|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | リソースの作成に使用するサブスクリプション ID。|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント ID。|
| client_secret | *xxxxxxxxxxxxxx* | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント シークレット。 |
| resource_group_name | *testrg* | ご利用のクラスターを含むリソース グループの名前。|
| cluster_name | *mykustocluster* | ご利用のクラスターの名前。|
| database_name | *mykustodatabase* | ご利用のクラスター内のターゲット データベースの名前。|
| data_connection_name | *myeventhubconnect* | データ接続の任意の名前。|
| table_name | *StormEvents* | ターゲット データベース内のターゲット テーブルの名前。|
| mapping_rule_name | *StormEvents_CSV_Mapping* | ターゲット テーブルに関連付けられている列マッピングの名前。|
| data_format | *csv* | メッセージのデータ形式。|
| event_hub_resource_id | *リソース ID* | インジェスト用のデータを保持しているイベント ハブのリソース ID。 |
| consumer_group | *$Default* | ご利用のイベント ハブのコンシューマー グループ。|
| location | *米国中部* | データ接続リソースの場所。|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../includes/data-explorer-data-connection-clean-resources-python.md)]

## <a name="next-steps"></a>次の手順

- [Data Explorer を使用して分析する](../../get-started-analyze-data-explorer.md)
- [Data Explorer プールを監視する](../data-explorer-monitor-pools.md)
