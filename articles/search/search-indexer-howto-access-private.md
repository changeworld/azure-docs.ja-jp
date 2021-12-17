---
title: プライベート エンドポイントを経由したインデクサー接続
titleSuffix: Azure Cognitive Search
description: インデクサー接続を構成して、プライベート エンドポイント経由で保護されている他の Azure リソースからコンテンツにアクセスできるようにします。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/13/2021
ms.openlocfilehash: 484f52656c5e49113d50f25a94a33b94ed886ab0
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359190"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>プライベート エンドポイントを経由したインデクサー接続の作成

> [!NOTE]
> [信頼された Microsoft サービス アプローチ](../storage/common/storage-network-security.md#trusted-microsoft-services)を使って、ストレージ アカウントの仮想ネットワークまたは IP 制限を回避できます。 また、検索サービスでストレージ アカウントのデータにアクセスできるようにすることもできます。 これを行うには、[信頼されたサービスの例外を使用した Microsoft Azure Storage アカウントのデータへの安全なアクセス](search-indexer-howto-access-trusted-service-exception.md)に関する記事を参照してください。
> 
> ただし、このアプローチを使用する場合、Azure Cognitive Search とストレージ アカウント間の通信は、セキュリティで保護された Microsoft バックボーン ネットワークで、ストレージ アカウントのパブリック IP アドレスを介して行われます。

Azure ストレージ アカウントなどの多くの Azure リソースは、仮想ネットワークの一覧からの接続を受け入れ、パブリック ネットワークからの外部接続を拒否するように構成できます。 インデクサーを使用して Azure Cognitive Search のデータにインデックスを作成し、データ ソースがプライベート ネットワーク上にある場合は、データに到達するために発信[プライベート エンドポイント接続](../private-link/private-endpoint-overview.md)を作成できます。

このインデクサーの接続方法には、次の 2 つの要件があります。

+ コンテンツまたはコードを提供する Azure リソースは、[Azure Private Link サービス](https://azure.microsoft.com/services/private-link/)に事前登録されている必要があります。

+ Azure Cognitive Search サービスは、Basic レベル以上である必要があります。 この機能は、Free レベルでは使用できません。 また、インデクサーにスキルセットがある場合、レベルは Standard 2 (S2) 以上である必要があります。 詳細については、[サービスの制限](search-limits-quotas-capacity.md#shared-private-link-resource-limits)に関する記事を参照してください。

## <a name="shared-private-link-resources-management-apis"></a>共有プライベート リンク リソース管理 API

Azure Cognitive Search API によって作成された、セキュリティで保護されたリソースのプライベート エンドポイントは、"*共有プライベート リンク リソース*" と呼ばれます。 これは、[Azure Private Link サービス](https://azure.microsoft.com/services/private-link/)と統合されているストレージ アカウントなどのリソースへのアクセスを "共有" しているためです。

Azure Cognitive Search では、その管理 REST API を通じて、Azure Cognitive Search インデクサーからのアクセスを構成するために使用できる [CreateOrUpdate](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update) 操作が提供されます。

一部のリソースへのプライベート エンドポイント接続を作成できるのは、検索管理 API のプレビュー バージョン (バージョン *2020-08-01-preview* 以降) を使用する場合のみです。これは、次の表で "*プレビュー*" として示されています。 "*プレビュー*" の指定のないリソースは、プレビューまたは一般提供されている API バージョン (*2020-08-01* 以降) を使用して作成できます。

次の表は、Azure Cognitive Search から発信プライベート エンドポイントを作成できる Azure リソースを示しています。 共有プライベート リンク リソースを作成するには、API に記述されているとおりに、**グループ ID** 値を入力します。 値は大文字と小文字が区別されます。

| Azure リソース | グループ ID |
| --- | --- |
| Azure Storage - Blob | `blob`|
| Azure Storage - Data Lake Storage Gen2 | `dfs` および `blob` |
| Azure Storage - Tables | `table`|
| Azure Cosmos DB - SQL API | `Sql`|
| Azure SQL データベース | `sqlServer`|
| Azure Database for MySQL (プレビュー) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (プレビュー) | `sites` |

発信プライベート エンドポイント接続がサポートされている Azure リソースは、[サポートされる API のリスト](/rest/api/searchmanagement/2021-04-01-preview/private-link-resources/list-supported)を使って照会することもできます。

この記事の残りの部分では、Azure portal (または必要に応じて [Azure CLI](/cli/azure/)) と [Postman](https://www.postman.com/) (または必要に応じて [curl](https://curl.se/) などの他の HTTP クライアント) を組み合わせて使用して、REST API の呼び出しをデモンストレーションします。

> [!NOTE]
> Azure Cognitive Search データ ソースなど、正しく機能するために複数の共有プライベート リンクの作成が必要な構成も存在します。 次のリストに、この要件に当てはまる構成と、それぞれに必要なグループ ID を記載します。
> * **Azure Data Lake Storage Gen2 データ ソース** - dfs、blob という groupID を持つ共有プライベート リンクを 1 つずつ、合計 2 つ作成します。
> * **ナレッジ ストアを設定しているスキルセット** - ナレッジ ストアに設定しているプロジェクションにより、1 つまたは 2 つの共有プライベート リンクが必要です。
>   * BLOB またはファイル プロジェクションを使用する場合は、blob という groupID を持つ共有プライベート リンクを 1 つ作成します。 
>   * テーブル プロジェクションを使用する場合は、table という groupID を持つ共有プライベート リンクを 1 つ作成します。 
>   * BLOB または ファイル プロジェクションとテーブル プロジェクションを使用する場合は、blob、table という groupID を持つ共有プライベート リンクを 1 つずつ、合計 2 つ作成します。 
> * **キャッシュを有効にしたインデクサー** - table、blob という groupID を持つ共有プライベート リンクを 1 つずつ、合計 2 つ作成します。

## <a name="set-up-indexer-connection-through-private-endpoint"></a>プライベート エンドポイントを使用してインデクサー接続を設定する

セキュリティで保護された Azure リソースへのプライベート エンドポイントを通じてインデクサー接続を設定するには、次の手順を使用します。

この記事の例は、次の前提に基づいています。
* 検索サービスの名前は _contoso-search_ です。これは、サブスクリプション ID _00000000-0000-0000-0000-000000000000_ を持つサブスクリプションの _contoso_ リソース グループに存在します。 
* この検索サービスのリソース ID は、 _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search_ です。

### <a name="step-1-secure-your-azure-resource"></a>手順 1: Azure リソースをセキュリティで保護する

アクセスを制限する手順は、リソースによって異なります。 以下のシナリオは、3 つの一般的な種類のリソースを示しています。

- シナリオ 1: Azure Storage

    Azure ストレージ アカウント ファイアウォールの設定例を次に挙げます。 このオプションをオンにしてページを空のままにすると、仮想ネットワークからのトラフィックは許可されないことを意味します。

    ![Azure Storage の [ファイアウォールと仮想ネットワーク] ペインのスクリーンショット。選択したネットワークへのアクセスを許可するオプションが表示されています。](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

- シナリオ 2: Azure Key Vault

    Azure Key Vault ファイアウォールの設定例を次に挙げます。
 
    ![Azure Key Vault の [ファイアウォールと仮想ネットワーク] ペインのスクリーンショット。選択したネットワークへのアクセスを許可するオプションが表示されています。](media\search-indexer-howto-secure-access\key-vault-firewall-noaccess.png)
    
- シナリオ 3: Azure Functions

    Azure Functions のファイアウォールでは、ネットワーク設定の変更は必要ありません。 これ以降のステップでは、Function に共有プライベート エンドポイントを作成すると、それ以降は Function の機能によって、プライベート リンクを使用するアクセスだけが許可されるようになります。

### <a name="step-2-create-a-shared-private-link-resource-to-the-azure-resource"></a>手順 2: Azure リソースへの共有プライベート リンク リソースを作成する

次のセクションでは、Azure portal または Azure CLI のいずれかを使用して共有プライベート リンク リソースを作成する方法について説明します。 

#### <a name="option-1-portal"></a>オプション 1: ポータル

> [!NOTE]
> ポータルでは、一般提供されているグループ ID 値を使用した共有プライベート エンドポイントの作成のみがサポートされます。 MySQL と Azure Functions については、後の「オプション 2」で説明されている Azure CLI の手順を使用します。

[共有プライベート アクセス] ブレードを使用して発信プライベート エンドポイント接続を作成するように Azure Cognitive Search に要求するには、[共有プライベート アクセスの追加] をクリックします。 右側に表示されるブレードで、[マイ ディレクトリ内の Azure リソースに接続します] または [リソース ID またはエイリアスを使って Azure リソースに接続します] を選択できます。

最初のオプション (推奨) を使用する場合、ブレードは適切な Azure リソースを選択するのに役立ち、リソースのグループ ID やリソースの種類などの他のプロパティが自動入力されます。

   ![[共有プライベート アクセスの追加] ペインのスクリーンショット。共有プライベート リンク リソースを作成するためのガイド付きエクスペリエンスが示されています。 ](media\search-indexer-howto-secure-access\new-shared-private-link-resource.png)

2 番目のオプションを使用する場合は、Azure リソース ID を手動で入力し、適切なグループ ID を選択できます。 グループ ID は、この記事の冒頭に記載されています。

![[共有プライベート アクセスの追加] ペインのスクリーンショット。共有プライベート リンク リソースを作成するための手動エクスペリエンスが示されています。 ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-manual.png)

#### <a name="option-2-azure-cli"></a>オプション 2: Azure CLI

または、[Azure CLI](/cli/azure/) を使用して次の API 呼び出しを実行することができます。 プレビュー段階にあるグループ ID を使用している場合は、2020-08-01-preview API バージョンを使用します。 たとえば、グループ ID *sites* および *mysqlServer* はプレビュー段階にあり、プレビュー API を使用する必要があります。

```dotnetcli
az rest --method put --uri https://management.azure.com/subscriptions/<search service subscription ID>/resourceGroups/<search service resource group name>/providers/Microsoft.Search/searchServices/<search service name>/sharedPrivateLinkResources/<shared private endpoint name>?api-version=2020-08-01 --body @create-pe.json
```

*create-pe.json* ファイルの内容の例を次に示します。

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

成功した場合、`202 Accepted` 応答が返されます。 発信プライベート エンドポイントを作成するプロセスは長時間実行される (非同期の) 操作です。 これには、次のリソースのデプロイが含まれます。

+ プライベート IP アドレスが割り当てられた、`"Pending"` 状態のプライベート エンドポイント。 このプライベート IP アドレスは、検索サービス固有のプライベート インデクサー実行環境の仮想ネットワークに割り当てられたアドレス空間から取得されます。 プライベート エンドポイントが承認されると、Azure Cognitive Search から Azure リソースへのすべての通信は、プライベート IP アドレスとセキュリティで保護されたプライベート リンク チャネルから行われます。

+ `groupId` に基づく、リソースの種類のプライベート DNS ゾーン。 このリソースをデプロイすると、プライベート リソースへの DNS 参照で、プライベート エンドポイントに関連付けられた IP アドレスが使用されるようになります。

プライベート エンドポイントを作成するリソースの種類に対して必ず正しい `groupId` を指定します。 不一致があると、失敗を示す応答メッセージが返されます。

### <a name="step-3-check-the-status-of-the-private-endpoint-creation"></a>手順 3: プライベート エンドポイントの作成の状態を確認する

この手順では、リソースのプロビジョニングの状態が "成功" に変更されていることを確認します。

#### <a name="option-1-portal"></a>オプション 1: ポータル

> [!NOTE]
> プロビジョニングの状態は、プレビュー段階の GA およびグループ ID の両方について、ポータルに表示されます。

ポータルに、共有プライベート エンドポイントの状態が表示されます。 次の例では、状態は "更新中" です。

![[共有プライベート アクセスの追加] ペインのスクリーンショット。リソースの作成が進行中であることが示されています。 ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-progress.png)

リソースが正常に作成されると、ポータル通知を受信し、リソースのプロビジョニング状態が "成功" に変わります。

![[共有プライベート アクセスの追加] ペインのスクリーンショット。リソースの作成が完了したことが示されています。 ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-success.png)

#### <a name="option-2-azure-cli"></a>オプション 2: Azure CLI

共有プライベート エンドポイントを作成するための `PUT` 呼び出しでは、次のような `Azure-AsyncOperation` ヘッダー値が返されます。

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

`Azure-AsyncOperationHeader` 値を手動でクエリすることで、状態をポーリングできます。

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01
```

### <a name="step-4-approve-the-private-endpoint-connection"></a>手順 4:プライベート エンドポイント接続を承認する

> [!NOTE]
> このセクションでは、Azure portal を使用して、接続先の Azure リソースに対するプライベート エンドポイントの承認フローを順を追って説明します。 または、ストレージ リソース プロバイダーを介して利用可能な [REST API](/rest/api/storagerp/privateendpointconnections) を使うこともできます。
>
> Azure Cosmos DB や Azure SQL Server などの他のプロバイダーは、プライベート エンドポイント接続を管理するための同様のストレージ リソース プロバイダー API を提供します。

1. Azure portal 内で、接続先の Azure リソースに移動し、 **[ネットワーク]** タブを選択します。次に、プライベート エンドポイント接続を一覧表示するセクションに移動します。 ストレージ アカウントの例を次に示します。 非同期操作が成功した後に、前の API 呼び出しからの要求メッセージを使用して、プライベート エンドポイント接続の要求を行う必要があります。

   ![Azure portal のスクリーンショット。[プライベート エンドポイント接続] ウィンドウが表示されています。](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Azure Cognitive Search によって作成されたプライベート エンドポイントを選択します。 **[プライベート エンドポイント]** 列で、前の API で指定した名前でプライベート エンドポイント接続を特定し、 **[承認]** を選択して、適切なメッセージを入力します。 メッセージの内容は重要ではありません。

   プライベート エンドポイント接続が次のスクリーンショットに示すように表示されていることを確認します。 ポータルで状態が更新されるまでに 1 から 2 分かかる場合があります。

   ![Azure portal のスクリーンショット。[プライベート エンドポイント接続] ウィンドウに [承認済み] の状態が表示されています。](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

プライベート エンドポイント接続要求が承認されると、トラフィックがプライベート エンドポイントを介してフロー "*できる*" ようになります。 プライベート エンドポイントが承認されると、Azure Cognitive Search によって、それに対して作成された DNS ゾーンに必要な DNS ゾーン マッピングが作成されます。

### <a name="step-5-query-the-status-of-the-shared-private-link-resource"></a>手順 5: 共有プライベート リンク リソースの状態を照会する

共有プライベート リンク リソースが承認後に更新されたことを確認するには、Azure portal で検索サービスの [共有プライベート アクセス] ブレードを再表示し、[接続状態] を確認します。

   ![Azure portal のスクリーンショット。"承認済み" の共有プライベート リンク リソースが示されています。](media\search-indexer-howto-secure-access\new-shared-private-link-resource-approved.png)

または、[GET API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get) を使用して "接続状態" を取得することもできます。

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01
```

これにより、JSON が返されます。接続状態は "properties" セクションの下の "status" として表示されます。 ストレージ アカウントの例を次に示します。

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

リソースの "プロビジョニング状態" (`properties.provisioningState`) が `Succeeded` で、"接続状態" (`properties.status`) が `Approved` である場合、共有プライベート リンク リソースが機能しており、プライベート エンドポイントを介して通信するようにインデクサーを構成できることを意味します。

### <a name="step-6-configure-the-indexer-to-run-in-the-private-environment"></a>手順 6:プライベート環境で実行されるようにインデクサーを構成する

> [!NOTE]
> この手順は、プライベート エンドポイント接続が承認される前に実行できます。 プライベート エンドポイント接続が承認されるまで、セキュリティで保護されたリソース (ストレージ アカウントなど) との通信を試行するすべてのインデクサーは、一時的なエラー状態になります。 新しいインデクサーの作成は失敗します。 プライベート エンドポイント接続が承認されるとすぐに、インデクサーはプライベート ストレージ アカウントにアクセスできます。

次の手順では、REST API を使用して、インデクサーをプライベート環境で実行するように構成する方法を示します。 ポータル内で JSON エディターを使用して実行環境を設定することもできます。

1. 通常どおりに、データ ソース定義、インデックス、およびスキルセット (使用している場合) を作成します。 共有プライベート エンドポイントを使用する場合、これらの定義にはプロパティがありません。

1. 前の手順で作成したデータ ソース、インデックス、スキルセットを指す[インデクサーを作成](/rest/api/searchservice/create-indexer)します。 また、インデクサーの構成プロパティ `executionEnvironment` を `private` に設定して、インデクサーがプライベート実行環境で実行されるように強制します。

    ```json
    {
        "name": "indexer",
        "dataSourceName": "blob-datasource",
        "targetIndexName": "index",
        "parameters": {
            "configuration": {
                "executionEnvironment": "private"
            }
        },
        "fieldMappings": []
    }
    ```

    Postman 内での要求の例を次に示します。
    
    ![Postman ユーザー インターフェイスでのインデクサーの作成を示すスクリーンショット。](media\search-indexer-howto-secure-access\create-indexer.png)    

インデクサーが正しく作成されると、プライベート エンドポイント接続を介してそれが Azure リソースに接続されます。 インデクサーの状態は、[インデクサーの状態 API](/rest/api/searchservice/get-indexer-status) を使用して監視できます。

> [!NOTE]
> 既存のインデクサーがある場合は、[PUT API](/rest/api/searchservice/create-indexer) で `executionEnvironment` を `private` に設定するか、ポータル内で JSON エディターを使用して、それらを更新できます。

## <a name="troubleshooting"></a>トラブルシューティング

+ インデクサーの作成が、"データ ソースの資格情報が無効です" という内容のエラー メッセージが表示されて失敗した場合は、プライベート エンドポイント接続がまだ "*承認*" されていないか、接続が機能していないことを意味します。 この問題を解決するには、次のようにします。 
  + [GET API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get) を使用して、共有プライベート リンク リソースの状態を取得します。 状態が "*承認済み*" の場合は、リソースの `properties.provisioningState` を確認します。 ここの状態が `Incomplete` の場合、リソースの基になる依存関係の一部がセットアップされなかったことを意味します。 共有プライベート リンク リソースを再作成するために `PUT` 要求を再発行すると、問題が解決されます。 再承認が必要になる場合があります。 リソースの状態を再確認して、問題が解決されたことを確認します。

+ `executionEnvironment` プロパティを設定せずにインデクサーを作成した場合、そのインデクサーの作成は成功する場合もありますが、その実行履歴には、インデクサーの実行が失敗したことが示されます。 この問題を解決するには、次のようにします。
  + [インデクサーを更新](/rest/api/searchservice/update-indexer)して、実行環境を指定します。

+ `executionEnvironment` プロパティを設定せずにインデクサーを作成し、そのインデクサーが正常に実行された場合、Azure Cognitive Search によってその実行環境が検索サービス固有の "*プライベート*" 環境であると判断されたことを意味します。 これは、インデクサーによって消費されるリソース、検索サービスの負荷、その他の要因などによって変わることがあり、後で失敗する可能性があります。 この問題を解決するには、次のようにします。
  + `executionEnvironment` プロパティを `private` に設定して、今後エラーが発生しないようにすることを強くお勧めします。

+ Azure portal でデータ ソースのネットワーク ページを表示していて、このデータ ソースにアクセスするために Azure Cognitive Search サービス用に作成したプライベート エンドポイントを選択した場合、"*アクセス不可*" のエラーが表示されることがあります。 これは予期されることです。 ターゲット サービスのポータル ページを使用して接続要求の状態を変更できますが、共有プライベート リンク リソースをさらに管理するには、Azure portal の検索サービスのネットワーク ページで共有プライベート リンク リソースを表示する必要があります。

[クォータと制限](search-limits-quotas-capacity.md)によって、作成できる共有プライベート リンク リソースの数が決まります。クォータと制限は、検索サービスの SKU に基づきます。

## <a name="next-steps"></a>次のステップ

以下で、プライベート エンドポイントについて詳しく学習してください。

+ [共有プライベート リンク リソースに関する問題のトラブルシューティング](troubleshoot-shared-private-link-resources.md)
+ [プライベート エンドポイントとは](../private-link/private-endpoint-overview.md)
+ [プライベート エンドポイントに必要な DNS 構成](../private-link/private-endpoint-dns.md)