---
title: Azure Cosmos DB の要求率が大きすぎる例外をトラブルシューティングする
description: 要求率が大きすぎる例外を診断して修正する方法について説明します。
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 08/25/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0c8ad195ce2e5288857595b042bb30cf6b5c8a6d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618420"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-429-exceptions"></a>Azure Cosmos DB の要求率が大きすぎる (429) 例外を診断してトラブルシューティングする
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

この記事には、SQL API のさまざまな 429 状態コード エラーの既知の原因と解決策が記載されています。 MongoDB 用 API を使用する場合、状態コード 16500 をデバッグする方法について、[MongoDB 用 API での一般的な問題のトラブルシューティング](../mongodb/error-codes-solutions.md)に関するページを参照してください。

"要求率が大きすぎる" という例外 (エラー コード 429) は、Azure Cosmos DB に対する要求がレート制限されていることを示します。

プロビジョニングされたスループットを使用する場合は、ワークロードに必要な、1 秒あたりの要求ユニット数 (RU/秒) で測定されるスループットを設定します。 読み取り、書き込み、クエリなどのサービスに対するデータベース操作では、一定量の要求ユニット (RU) が消費されます。 詳細については、[要求ユニット](../request-units.md)に関する記事を参照してください。

ある 1 秒間で、プロビジョニングされた要求ユニット数を超える要求ユニットが操作により消費されると、Azure Cosmos DB から 429 例外が返されます。 1 秒ごとに、使用可能な要求ユニットの数がリセットされます。

RU/秒を変更するアクションを実行する前に、レート制限の根本原因を理解し、根底にある問題を解決することが重要です。  

さまざまな種類の 429 例外に対応するさまざまなエラー メッセージがあります。
- [Request rate is large. More Request Units may be needed, so no changes were made. (要求率が大きいです。追加の要求ユニットが必要な場合があります。このため、変更は行われませんでした。)](#request-rate-is-large)
- [The request did not complete due to a high rate of metadata requests. (メタデータ要求のレートが高いため、要求は完了しませんでした。)](#rate-limiting-on-metadata-requests)
- [The request did not complete due to a transient service error. (一時的なサービス エラーが原因で、要求は完了しませんでした。)](#rate-limiting-due-to-transient-service-error)


## <a name="request-rate-is-large"></a>要求率が大きいです
これは最も一般的なシナリオです。 これは、データに対する操作で使用された要求ユニット数が、プロビジョニングされた RU/秒の数を超えた場合に発生します。 

### <a name="step-1-check-the-metrics-to-determine-the-percentage-of-requests-with-429-error"></a>手順 1: メトリックを調べて、429 エラーが発生した要求のパーセンテージを確認する
429 エラー メッセージが表示されることは、必ずしもデータベースまたはコンテナーに問題があることを意味しません。

#### <a name="how-to-investigate"></a>調査方法

429 が発生したデータベースまたはコンテナーに対する要求のパーセンテージを、成功した要求の総数と比較して特定します。 Azure Cosmos DB アカウント ブレードで、 **[分析情報]**  >  **[要求]**  >  **[状態コードごとの要求の合計数]** に移動します。 フィルター処理して、特定のデータベースとコンテナーを表示します。 

Azure Cosmos DB クライアント SDK とデータ インポート ツール (Azure Data Factory、バルク エグゼキューター ライブラリなど) は、429 が発生すると、既定では要求を自動的に再試行します。 通常、再試行は最大で 9 回です。 このため、メトリックで 429 が表示されている一方で、これらのエラーがアプリケーションに返されていない場合もあります。 

:::image type="content" source="media/troubleshoot-request-rate-too-large/insights-429-requests.png" alt-text="429 および 2xx 要求の数を示す [状態コードごとの要求の合計数] グラフ。":::


#### <a name="recommended-solution"></a>推奨される解決策
一般的に、運用ワークロードで、要求の 1 から 5% で 429 が発生し、エンド ツー エンドの待機時間が許容範囲内である場合、これは RU/秒が完全に利用されているという正常な兆候です。 必要な操作はありません。 それ以外の場合は、次のトラブルシューティング手順に進みます。

### <a name="step-2-determine-if-there-is-a-hot-partition"></a>手順 2: ホット パーティションがあるかどうかを特定する
ホット パーティションが発生するのは、要求量の多さが原因で、1 つ以上の論理パーティション キーによって消費される RU/秒の合計量が不均衡な場合です。 この原因として考えられるのは、要求を均等に分散させないパーティション キーの設計です。 この結果、多くの要求が論理 (物理を意味する) パーティションの小規模なサブセットに送信され、それらが "ホット" になります。 論理パーティションのすべてのデータは 1 つの物理パーティションに存在し、それらの物理パーティション間で RU/秒の合計量が均等に分配されます。このため、ホット パーティションで 429 が発生し、スループットが非効率的に使用されることになります。 

ホット パーティションの原因となるパーティション分割戦略の例を次に示します。
- 書き込みが多いワークロード用の IoT デバイス データを保存する、`date` でパーティション分割されたコンテナーがあります。 1 つの日付のすべてのデータが、同じ論理および物理パーティションに存在します。 毎日書き込まれるすべてのデータが同じ日付であるため、毎日ホット パーティションが発生します。 
    - このシナリオでは、代わりに、`id` (GUID またはデバイス ID) などのパーティション キー、または `id` と `date` を組み合わせた[合成パーティション キー](./synthetic-partition-keys.md)を使用すると、値のカーディナリティが高まり、要求量がより適切に分配されます。
- `tenantId` によってパーティション分割されたコンテナーを使用するマルチテナント シナリオがあります。 1 つのテナントが他のテナントよりも大幅にアクティブな場合、ホット パーティションが発生します。 たとえば、最大のテナントのユーザー数が 100,000 人で、ほとんどのテナントのユーザー数が 10 人未満の場合、`tenantID` でパーティション分割すると、ホット パーティションが発生します。 
    - この前述のシナリオでは、`UserId` などのより詳細なプロパティによってパーティション分割された、最大テナント用の専用コンテナーを使用することを検討します。 
    
#### <a name="how-to-identify-the-hot-partition"></a>ホット パーティションを識別する方法

ホット パーティションがあるかどうかを確認するには、 **[分析情報]**  >  **[スループット]**  >  **[PartitionKeyRangeID ごとの正規化された RU 消費量 (%)]** に移動します。 フィルター処理して、特定のデータベースとコンテナーを表示します。 

各 PartitionKeyRangeId は、1 つの物理パーティションにマップされます。 ある PartitionKeyRangeId の正規化された RU 消費量が他のものよりも大幅に高い場合 (たとえば、一方が常に 100% で、他方が 30% 未満の場合)、これは、ホット パーティションの兆候と考えることができます。 正規化された RU 消費量メトリックの詳細については、[こちら](../monitor-normalized-request-units.md)を参照してください。

:::image type="content" source="media/troubleshoot-request-rate-too-large/split-norm-utilization-by-pkrange-hot-partition.png" alt-text="ホット パーティションが含まれる [PartitionKeyRangeID ごとの正規化された RU 消費量] グラフ。":::

最も RU/秒を消費している論理パーティション キーを確認するには、[Azure 診断ログ](../cosmosdb-monitor-resource-logs.md)を使用します。 このサンプル クエリでは、各論理パーティション キーで 1 秒間に消費された合計要求ユニット数が合計されます。 

> [!IMPORTANT]
> 診断ログを有効にすると、Log Analytics サービスに対して別途料金が発生します。これは、取り込まれたデータの量に基づいて課金されます。 診断ログは、デバッグのための限られた時間だけ有効にし、不要になったら無効にすることをお勧めします。 詳細については、[価格](https://azure.microsoft.com/pricing/details/monitor/)に関するページをご覧ください。

```kusto
AzureDiagnostics
| where TimeGenerated >= ago(24hour)
| where Category == "PartitionKeyRUConsumption"
| where collectionName_s == "CollectionName" 
| where isnotempty(partitionKey_s)
// Sum total request units consumed by logical partition key for each second
| summarize sum(todouble(requestCharge_s)) by partitionKey_s, operationType_s, bin(TimeGenerated, 1s)
| order by sum_requestCharge_s desc
```
このサンプル出力では、特定の分において、"Contoso" という値の論理パーティション キーで約 12,000 RU/秒が消費された一方で、"Fabrikam" という値の論理パーティション キーでは 600 RU/秒未満が消費されたことが示されています。 レート制限が発生した期間中にこのパターンが一貫して見られる場合、これはホット パーティションを示します。 

:::image type="content" source="media/troubleshoot-request-rate-too-large/hot-logical-partition-key-results.png" alt-text="1 秒あたりの要求ユニット数を最も多く消費する論理パーティション キー。":::

> [!TIP]
> どのワークロードでも、論理パーティション間で要求量は自然に変化します。 ホット パーティションの原因が、パーティション キーの選択による根本的な歪みであるか (この場合、キーの変更が必要になることがあります)、またはワークロード パターンの自然な変動による一時的な急増であるか特定する必要があります。

#### <a name="recommended-solution"></a>推奨される解決策
[適切なパーティション キーを選択する方法](../partitioning-overview.md#choose-partitionkey)に関するガイダンスを確認します。

レート制限された要求のパーセンテージが高く、ホット パーティションがない場合:
- クライアント SDK、Azure portal、PowerShell、CLI、または ARM テンプレートを使用して、データベースまたはコンテナーで [RU/秒を増やす](../set-throughput.md)ことができます。 「[プロビジョニングされたスループットのスケーリングに関するベスト プラクティス](../scaling-provisioned-throughput-best-practices.md)」に従って、設定する適切な RU/s を決定します。

レート制限された要求のパーセンテージが高く、根底にホット パーティションがある場合:
-  長期的には、最適なコストとパフォーマンスのために、**パーティション キーの変更** を検討します。 パーティション キーは更新できないため、この場合、異なるパーティション キーを持つ新しいコンテナーにデータを移行する必要があります。 Azure Cosmos DB では、この目的のために[ライブ データ移行ツール](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)がサポートされています。
- 短期的には、RU/秒を一時的に増やして、ホット パーティションに対するスループットを向上させることができます。 これは、RU/秒のオーバープロビジョニングとコストの増加につながるため、長期的な戦略としては推奨されません。 

> [!TIP]
>  スループットを増やす場合、スケールアップする先の RU/秒の数によって、スケールアップ操作は即座に完了するか、完了まで最大で 5 から 6 時間必要になります。 非同期のスケールアップ操作 (追加の物理パーティションをプロビジョニングするために Azure Cosmos DB が必要になります) をトリガーせずに設定できる RU/秒の最大数を確認するには、個別の PartitionKeyRangeId の数に 100,000 RU/秒を乗算します。 たとえば、30,000 RU/秒がプロビジョニングされ、5 つの物理パーティションがある場合 (物理パーティションごとに 6000 RU/秒が割り当てられている)、即座のスケールアップ操作で 50,000 RU/秒 (物理パーティションあたり 10,000 RU/秒) まで増やすことができます。 50,000 RU/秒よりも多い数に増やすには、非同期のスケールアップ操作が必要です。

### <a name="step-3-determine-what-requests-are-returning-429s"></a>手順 3: 429 を返す要求を特定する

#### <a name="how-to-investigate-requests-with-429s"></a>429 を発生させる要求を調査する方法
[Azure 診断ログ](../cosmosdb-monitor-resource-logs.md)を使用して、429 を返す要求と、それらによって消費された RU の数を特定します。 このサンプル クエリでは、分レベルで集計されます。 

> [!IMPORTANT]
> 診断ログを有効にすると、Log Analytics サービスに対して別途料金が発生します。これは、取り込まれたデータの量に基づいて課金されます。 診断ログは、デバッグのための限られた時間だけ有効にし、不要になったら無効にすることをお勧めします。 詳細については、[価格](https://azure.microsoft.com/pricing/details/monitor/)に関するページをご覧ください。

```kusto
AzureDiagnostics
| where TimeGenerated >= ago(24h)
| where Category == "DataPlaneRequests"
| summarize throttledOperations = dcountif(activityId_g, statusCode_s == 429), totalOperations = dcount(activityId_g), totalConsumedRUPerMinute = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, OperationName, requestResourceType_s, bin(TimeGenerated, 1min)
| extend averageRUPerOperation = 1.0 * totalConsumedRUPerMinute / totalOperations 
| extend fractionOf429s = 1.0 * throttledOperations / totalOperations
| order by fractionOf429s desc
```
たとえば、このサンプル出力では、各分で、ドキュメント作成要求の 30% がレート制限されており、各要求で平均 17 個の RU が消費されています。
:::image type="content" source="media/troubleshoot-request-rate-too-large/throttled-requests-diagnostic-logs-results.png" alt-text="診断ログ内の 429 を発生させた要求。":::

#### <a name="recommended-solution"></a>推奨される解決策
##### <a name="429s-on-create-replace-or-upsert-document-requests"></a>ドキュメントの作成、置換、またはアップサート要求での 429
- SQL API では、すべてのプロパティに既定でインデックスが付けられます。 必要なプロパティにのみインデックスを付けるように[インデックス作成ポリシー](../index-policy.md)を調整します。
これにより、ドキュメントの作成操作ごとに必要となる要求ユニットの数が減少し、429 が表示される可能性が低くなります。また、プロビジョニングされた同じ量の RU/秒に対して、1 秒あたりにより多くの操作を実行できます。 

##### <a name="429s-on-query-document-requests"></a>ドキュメントのクエリ要求での 429
- ガイダンスに従って、[RU 料金が高い場合のクエリのトラブルシューティング](troubleshoot-query-performance.md#querys-ru-charge-is-too-high)を行います。

##### <a name="429s-on-execute-stored-procedures"></a>ストアド プロシージャの実行での 429
- [ストアド プロシージャ](stored-procedures-triggers-udfs.md)は、パーティション キー値に対する書き込みトランザクションが必要な操作に適しています。 多くの読み取りまたはクエリ操作では、ストアド プロシージャを使用しないことをお勧めします。 最適なパフォーマンスを実現するために、これらの読み取りまたはクエリ操作は、Cosmos SDK を使用してクライアント側で実行する必要があります。 

## <a name="rate-limiting-on-metadata-requests"></a>メタデータ要求に対するレート制限

メタデータのレート制限は、データベース、コンテナー、またはその両方で大量のメタデータ操作を実行すると発生する可能性があります。 メタデータ操作には、以下が含まれます。
- コンテナーまたはデータベースの作成、読み取り、更新、または削除
- Cosmos アカウント内のデータベースまたはコンテナーの一覧表示
- 現在プロビジョニングされているスループットを確認するためのオファーに対するクエリ 

これらの操作には、システムで予約されている RU 制限があるため、データベースまたはコンテナーの RU/秒をプロビジョニングして増やしても、影響はありません。したがって、これは推奨されません。 [メタデータ操作の制限](../concepts-limits.md#metadata-request-limits)に関するページを参照してください。

#### <a name="how-to-investigate"></a>調査方法
**[分析情報]**  >  **[システム]**  >  **[状態コードごとのメタデータ要求数]** に移動します。 必要に応じて、フィルター処理して特定のデータベースとコンテナーを表示します。 

:::image type="content" source="media/troubleshoot-request-rate-too-large/metadata-throttling-insights.png" alt-text="[分析情報] 内の [状態コードごとのメタデータ要求数] グラフ。":::

#### <a name="recommended-solution"></a>推奨される解決策
- アプリケーションでメタデータ操作を実行する必要がある場合、これらの要求を低いレートで送信するために、バックオフ ポリシーを実装することを検討します。 

- 静的な Cosmos DB クライアント インスタンスを使用します。 DocumentClient または CosmosClient が初期化されると、Cosmos DB SDK により、整合性レベル、データベース、コンテナー、パーティション、オファーに関する情報など、アカウントに関するメタデータがフェッチされます。 この初期化では多数の RU が消費される可能性があるため、頻繁には実行しないでください。 単一 DocumentClient インスタンスを、お使いのアプリケーションの有効期間中に使用します。

- データベースとコンテナーの名前をキャッシュします。 構成からデータベースとコンテナーの名前を取得するか、開始時にそれらをキャッシュします。 ReadDatabaseAsync/ReadDocumentCollectionAsync や CreateDatabaseQuery/CreateDocumentCollectionQuery などの呼び出しでは、サービスに対するメタデータ呼び出しが行われ、システムで予約されている RU 制限から消費されます。 これらの操作は、頻繁には実行しないでください。

## <a name="rate-limiting-due-to-transient-service-error"></a>一時的なサービス エラーによるレート制限

この 429 エラーは、要求で一時的なサービス エラーが発生した場合に返されます。 データベースまたはコンテナーに対して RU/秒を増やしても影響はありません。したがって、これは推奨されません。

#### <a name="recommended-solution"></a>推奨される解決策
要求をやり直してください。 エラーが数分間続く場合は、[Azure portal](https://portal.azure.com/) からサポート チケットを提出してください。

## <a name="next-steps"></a>次の手順
* データベースまたはコンテナーの[正規化された RU/秒の消費を監視する](../monitor-normalized-request-units.md)。
* Azure Cosmos DB .NET SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)。
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET v2](performance-tips.md) のパフォーマンス ガイドラインを確認する。
* Azure Cosmos DB Java v4 SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-java-sdk-v4-sql.md)。
* [Java v4 SDK](performance-tips-java-sdk-v4-sql.md) のパフォーマンス ガイドラインを確認する。
