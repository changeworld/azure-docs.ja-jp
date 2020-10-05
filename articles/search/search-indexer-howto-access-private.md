---
title: プライベート エンドポイントを介してセキュリティで保護されたリソースにアクセスするインデクサー
titleSuffix: Azure Cognitive Search
description: インデクサーがセキュリティで保護されたリソースと通信するためのプライベート エンドポイントの設定について説明するハウツー ガイド
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: e9944e95dd452cd00e63280ad8002141591f521e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90971420"
---
# <a name="accessing-secure-resources-via-private-endpoints"></a>プライベート エンドポイントを介してセキュリティで保護されたリソースにアクセスする

Azure リソース (データ ソースとして使用されるストレージ アカウントなど) は、特定の仮想ネットワークの一覧からのみアクセスできるように構成できます。 また、"公衆ネットワーク" へのアクセスを許可しないように構成することもできます。
お客様は、インデクサーを使用してこのようなデータ ソースからデータに安全にアクセスするために、(発信) [プライベート エンドポイント接続](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)を作成するように Azure Cognitive Search に要求できます。

## <a name="shared-private-link-resources-management-apis"></a>共有プライベート リンク リソース管理 API

"セキュリティで保護された" リソースにアクセスするために、お客様の要求に応じて Azure Cognitive Search によって作成されたプライベート エンドポイントは、"*共有プライベート リンク リソース*" と呼ばれます。 お客様は、[Azure Private Link サービス](https://azure.microsoft.com/services/private-link/)にオンボードされたリソース (ストレージ アカウントなど) へのアクセスを "共有" します。

Azure Cognitive Search は、検索管理 API を通じて、[共有プライベート リンク リソースを作成または更新](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate)する機能を提供します。 この API を他の "*共有プライベート リンク リソース*" 管理 API と共に使用して、Azure Cognitive Search インデクサーからのセキュリティで保護されたリソースへのアクセスを構成します。

一部のリソースへのプライベート エンドポイント接続は、次の表の "プレビュー" タグで示されている検索管理 API のプレビュー バージョン (`2020-08-01-Preview`) を介してのみ作成できます。 "プレビュー" タグがないリソースは、プレビュー API と GA API (`2020-08-01`) の両方を介して作成できます。

Azure Cognitive Search から発信プライベート エンドポイントを作成できる Azure リソースの一覧を次に示します。 次の表に一覧表示されている `groupId` は、共有プライベート リンク リソースを作成するために、API で正確に (大文字と小文字を区別して) 使用する必要があります。

| Azure リソース | グループ ID |
| --- | --- |
| Azure Storage - BLOB (または) ADLS Gen 2 | `blob`|
| Azure Storage - Tables | `table`|
| Azure Cosmos DB - SQL API | `Sql`|
| Azure SQL データベース | `sqlServer`|
| Azure Database for MySQL (プレビュー) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (プレビュー) | `sites` |

発信プライベート エンドポイント接続がサポートされている Azure リソースの一覧は、[List Supported API](https://docs.microsoft.com/rest/api/searchmanagement/privatelinkresources/listsupported) を介して照会することもできます。

このガイドでは、[ARMClient](https://github.com/projectkudu/ARMClient) と [Postman](https://www.postman.com/) を組み合わせて使用して、REST API の呼び出しをデモンストレーションします。

> [!NOTE]
> このガイドでは、検索サービスの名前が __contoso-search__ で、これが、サブスクリプション ID が __00000000-0000-0000-0000-000000000000__ のサブスクリプションのリソース グループ __contoso__ に存在すると仮定します。 この検索サービスのリソース ID は `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search` です。

このガイドの残りの部分では、__contoso-search__ サービスを構成して、そのインデクサーがセキュリティで保護されたストレージ アカウント `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage` からデータにアクセスできるようにする方法について説明します。

## <a name="securing-your-storage-account"></a>ストレージ アカウントをセキュリティで保護する

[特定のサブネットからのアクセスのみを許可する](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network)ように、ストレージ アカウントを構成します。 Azure portal で、このオプションをオンにして、設定を空のままにすると、どの仮想ネットワークからのトラフィックも許可されないことを意味します。

   ![仮想ネットワーク アクセス](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png "仮想ネットワーク アクセス")

> [!NOTE]
> [信頼された Microsoft サービス アプローチ](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)を使用すると、このようなストレージ アカウントでの仮想ネットワークまたは IP の制限を回避できます。また、[ハウツー ガイド](search-indexer-howto-access-trusted-service-exception.md)の説明に従って、検索サービスがストレージ アカウントのデータにアクセスできるようにすることが可能です。 ただし、このアプローチを使用する場合、Azure Cognitive Search とストレージ アカウント間の通信は、セキュリティで保護された Microsoft バックボーン ネットワークで、ストレージ アカウントのパブリック IP アドレスを介して行われます。

## <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>手順 1:ストレージ アカウントに対して共有プライベート リンク リソースを作成する

次の API 呼び出しを実行して、ストレージ アカウントへの発信プライベート エンドポイント接続を作成するように Azure Cognitive Search に要求します。

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

`create-pe.json` ファイル (API への要求本文を表す) の内容は次のとおりです。

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

成功した場合は `202 Accepted` 応答が返されます。発信プライベート エンドポイントを作成するプロセスは長時間実行される (非同期の) 操作です。 これには、次のリソースのデプロイが含まれます。

1. プライベート IP アドレスが割り当てられた、`"Pending"` 状態のプライベート エンドポイント。 このプライベート IP アドレスは、検索サービス固有のプライベート インデクサー実行環境の仮想ネットワークに割り当てられたアドレス空間から取得されます。 プライベート エンドポイントが承認されると、Azure Cognitive Search からストレージ アカウントへのすべての通信は、プライベート IP アドレスとセキュリティで保護されたプライベート リンク チャネルから行われます。
2. `groupId` に基づく、リソースの種類のプライベート DNS ゾーン。 これにより、プライベート リソースへの DNS 参照で、プライベート エンドポイントに関連付けられた IP アドレスが使用されるようになります。

プライベート エンドポイントを作成するリソースの種類に対して正しい `groupId` を指定していることを確認します。 不一致があると、失敗を示す応答メッセージが返されます。

すべての非同期 Azure 操作と同様に、`PUT` の呼び出しにより、次のような `Azure-AsyncOperation` ヘッダー値が返されます。

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

この URI を定期的にポーリングして、操作の状態を取得できます。 続行する前に、共有プライベート リンク リソースの操作状態が終了状態 (つまり `succeeded`) になるまで待機することをお勧めします。

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

## <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>手順 2a:ストレージ アカウントのプライベート エンドポイント接続を承認する

> [!NOTE]
> このセクションでは、Azure portal を使用して、ストレージに対するプライベート エンドポイントの承認フローを順を追って説明します。 ストレージ リソース プロバイダー (RP) を介して使用できる [REST API](https://docs.microsoft.com/rest/api/storagerp/privateendpointconnections) も代わりに使用できます。
>
> CosmosDB や Azure SQL Server などの他のプロバイダーも、プライベート エンドポイント接続を管理するための同様の RP API を提供しています。

Azure portal でストレージ アカウントの **[プライベート エンドポイント接続]** タブに移動します。 (非同期操作が__成功__した後に) 前の API 呼び出しからの要求メッセージを使用して、プライベート エンドポイント接続の要求を行う必要があります。

   ![プライベート エンドポイント (承認)](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png "プライベート エンドポイント (承認)")

Azure Cognitive Search によって作成されたプライベート エンドポイントを選択し ([プライベート エンドポイント] 列を使用して、前の API で指定した名前によってプライベート エンドポイント接続を識別します)、該当するメッセージで [承認] を選択します (メッセージは重要ではありません)。 プライベート エンドポイント接続が次のように表示されていることを確認します (ポータルで状態が更新されるまで、1 分から 2 分かかることがあります)。

![プライベート エンドポイント (承認済み)](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png "プライベート エンドポイント (承認済み)")

プライベート エンドポイント接続要求が承認されると、トラフィックがプライベート エンドポイントを介してフロー "*できる*" ようになります。 プライベート エンドポイントが承認されると、Azure Cognitive Search によって、それに対して作成された DNS ゾーンに必要な DNS ゾーン マッピングが作成されます。

## <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>手順 2b:共有プライベート リンク リソースの状態を照会する

 共有プライベート リンク リソースが承認後に更新されたことを確認するために、[GET API](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get) を使用してその状態を取得します。

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

リソースの `properties.provisioningState` が `Succeeded` で、`properties.status` が `Approved` である場合、共有プライベート リンク リソースが機能しており、プライベート エンドポイントを介して通信するようにインデクサーを構成できます。

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

## <a name="step-3-configure-indexer-to-run-in-the-private-environment"></a>手順 3:プライベート環境で実行されるようにインデクサーを構成する

> [!NOTE]
> この手順は、プライベート エンドポイント接続が承認される前でも実行できます。 プライベート エンドポイント接続が承認されるまで、セキュリティで保護されたリソース (ストレージ アカウントなど) との通信を試行するすべてのインデクサーは、一時的なエラー状態になります。 新しいインデクサーの作成は失敗します。 プライベート エンドポイント接続が承認されるとすぐに、インデクサーはプライベート ストレージ アカウントにアクセスできるようになります。

1. セキュリティで保護されたストレージ アカウントと、ストレージ アカウント内の適切なコンテナーを指す[データ ソース](https://docs.microsoft.com/rest/api/searchservice/create-data-source)を作成します。 Postman を介して実行されるこの要求を次に示します。
![データ ソースの作成](media\search-indexer-howto-secure-access\create-ds.png "データ ソースの作成")

2. 同様に[インデックスを作成](https://docs.microsoft.com/rest/api/searchservice/create-index)し、必要に応じて REST API を使用して[スキルセットを作成](https://docs.microsoft.com/rest/api/searchservice/create-skillset)します。

3. 上で作成したデータ ソース、インデックス、スキルセットを指す[インデクサーを作成](https://docs.microsoft.com/rest/api/searchservice/create-indexer)します。 また、インデクサーの構成プロパティ `executionEnvironment` を `"Private"` に設定して、インデクサーがプライベート実行環境で実行されるように強制します。
![インデクサーの作成](media\search-indexer-howto-secure-access\create-idr.png "インデクサーの作成")

インデクサーが正しく作成され、プライベート エンドポイント接続を介してストレージ アカウントからのコンテンツのインデックス作成が進行中である必要があります。 インデクサーの状態は、[Indexer Status API](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) を使用して監視できます。

> [!NOTE]
> 既存のインデクサーがある場合は、[PUT API](https://docs.microsoft.com/rest/api/searchservice/create-indexer) で `executionEnvironment` を `"Private"` に設定してそれを更新するだけです。

## <a name="troubleshooting-issues"></a>問題のトラブルシューティング

- インデクサーを作成するときに、"データ ソースの資格情報が無効です" という内容のエラー メッセージが表示され、作成が失敗した場合は、プライベート エンドポイント接続が "*承認*" されていないか、機能していないことを意味します。
[GET API](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get) を使用して、共有プライベート リンク リソースの状態を取得します。 "*承認*" されている場合は、リソースの `properties.provisioningState` を確認します。 `Incomplete` の場合、リソースの基礎となる依存関係の一部がプロビジョニングに失敗したことを意味します。`PUT` 要求を再実行して、共有プライベート リンク リソースを "再作成" して問題を解決する必要があります。 再承認が必要な場合もあります。検証するために、リソースの状態をもう一度確認します。
- `executionEnvironment` を設定せずにインデクサーを作成した場合、そのインデクサーの作成は成功する場合もありますが、その実行履歴には、インデクサーの実行が失敗したことが示されます。 実行環境を指定するには、[インデクサーを更新](https://docs.microsoft.com/rest/api/searchservice/update-indexer)する必要があります。
- `executionEnvironment` を設定せずにインデクサーを作成し、そのインデクサーが正常に実行された場合、Azure Cognitive Search によってその実行環境が検索サービス固有の "プライベート" 環境であると判断されたことを意味します。 ただし、これは、さまざまな要因 (インデクサーが消費するリソース、検索サービスへの負荷など) によって変わる可能性があり、後で失敗する可能性もあります。今後失敗しないように `executionEnvironment` を `"Private"` に設定することを強くお勧めします。
- [クォータと制限](search-limits-quotas-capacity.md)によって、作成できる共有プライベート リンク リソースの数が決まります。クォータと制限は、検索サービスの SKU に基づきます。

## <a name="next-steps"></a>次のステップ

以下で、プライベート エンドポイントについて詳しく学習してください。

- [プライベート エンドポイントとは](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
- [プライベート エンドポイントに必要な DNS 構成](https://docs.microsoft.com/azure/private-link/private-endpoint-dns)