---
title: Azure Cosmos DB Cassandra API のサーバー診断
description: この記事では、Azure Cosmos DB の Cassandra API の一般的なエラー コードと、Log Analytics を使用してトラブルシューティングを行う方法について説明します
author: IriaOsara
ms.author: IriaOsara
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: troubleshooting
ms.date: 10/12/2021
ms.custom: template-how-to
ms.openlocfilehash: bbef336e16623113c45929710e49101bddfdf64e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092517"
---
# <a name="server-diagnostics-for-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API のサーバー診断
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Log Analytics は、Cassandra API アカウントでサーバー診断を実行するのに役立つ、Azure portal にあるツールです。 Azure Monitor ログによって収集されたデータからログ クエリを実行し、その結果を対話形式で分析します。 Log Analytics クエリから取得したレコードは、データに関するさまざまな分析情報を得るのに役立ちます。

## <a name="prerequisites"></a>前提条件

- [Log Analytics ワークスペース](../../azure-monitor/logs/quick-create-workspace.md)を作成する。
- [診断設定](../cosmosdb-monitor-resource-logs.md)を作成する。
- Cassandra API アカウントで[ログ分析](../../azure-monitor/logs/log-analytics-overview.md)を開始する。

## <a name="use-log-analytics"></a>Log Analytics の使用
ログ分析のセットアップが完了したら、ログの探索を開始して、より多くの分析情報を取得できます。

### <a name="explore-data-plane-operations"></a>データ プレーン操作を探索する
CDBCassandraRequests テーブルを使用して、お使いの Cassandra API アカウントのデータ プレーン操作を確認します。 上位 N 件 (10) の使用要求を確認し、行われた各要求に関する詳細情報を取得するサンプル クエリです。

```Kusto
CDBCassandraRequests
| where RequestCharge  > 0
| project DatabaseName, CollectionName, DurationMs, OperationName, ActivityId, ErrorCode, RequestCharge, PIICommandText 
| order by RequestCharge
| take 10
```

#### <a name="error-codes-and-possible-solutions"></a>エラー コードと考えられる解決策
|状態コード | エラー コード           | 説明  |
|------------|----------------------|--------------|
| 200 | -1 | 成功 |
| 400 | 8704 | クエリは正しいが、構文が無効です。 |
| 400 | 8192 | 送信されたクエリに構文エラーがあります。 クエリを見直してください。 |
| 400 | 8960 | 構成の問題が原因でクエリが無効です。 |
| 401 |8448 | ログに記録されたユーザーには、クエリを実行するための適切なアクセス許可がありません。 |
| 403 | 8448 | ユーザーが要求を実行するために必要なアクセス許可を持っていない可能性があるために、応答が拒否されました。 |
| 404 | 5376 | 応答が見つからなかった結果として、書き込み要求中にタイムアウト以外の例外が発生しました。 |
| 405 | 0 | サーバー側の Cassandra エラー。 このエラーはほとんど発生しません。サポート チケットを開いてください。 |
| 408 | 4608 | 読み取り要求中のタイムアウト。 |
| 408 | 4352 | 書き込み serviceRequest 中のタイムアウト例外。 |
| 409 | 9216 | 既に存在するキースペースまたはテーブルの作成が試行されました。 |
| 412 | 5376 | 前提条件のエラー。 データ整合性を確保するために、読み取り応答が true であることに基づいて書き込み要求が保証されます。 タイムアウト以外の書き込み要求例外が返されます。 |
| 413 | 5376 | 書き込み要求中のこのタイムアウト以外の例外は、ペイロードが大きすぎる可能性があることが原因です。 現時点では、1 行あたり 2 MB の制限があります。 |
| 417 | 9472 | この例外は、準備されたステートメントがサーバー ノードにキャッシュされていない場合にスローされます。 一時的または非ブロッキングです。 |
| 423 | 5376 | 現在処理されている書き込み要求のため、ロックがあります。 |
| 429 | 4097| オーバーロード例外は、RU 不足または高い要求レートの結果として発生します。 より高いボリューム要求を処理するために、RU の追加が必要な可能性があります。 ネイティブの Cassandra では、これは、いずれかの VM の CPU が十分ではないと解釈できます。 現在のデータ モデルを確認して、ホット パーティションの原因となっている可能性がある過度のスキューが存在しないことを確認してください。 |
| 449 | 5376 | 同時実行例外。 これは、特定の行に対して一度に 1 つの書き込み更新のみを保証するために発生します。 |
| 500 | 0 | サーバー cassandraError: 予期しない事象が発生しました。 サーバー側のバグを示しています。 |
| 503 | 4096 | Service unavailable. (サービス利用不可。) |
|   | 256 | これは、無効な接続資格情報が原因である可能性があります。 接続資格情報を確認してください。 |
|   | 10 | クライアント メッセージによってトリガーされたプロトコル違反。 たとえば、スタートアップ メッセージが送信される前に送信されたクエリ メッセージです。 |

### <a name="troubleshoot-query-consumption"></a>クエリの使用に関するトラブルシューティング
CDBPartitionKeyRUConsumption テーブルには、各物理パーティション内の各リージョンの論理キーに対する要求ユニット (RU) の消費に関する詳細が含まれています。

```Kusto
CDBPartitionKeyRUConsumption 
| summarize sum(todouble(RequestCharge)) by PartitionKey, PartitionKeyRangeId
| render columnchart
 ```

### <a name="explore-control-plane-operations"></a>コントロール プレーン操作の探索
CBDControlPlaneRequests テーブルには、Cassandra API アカウントに特化した、コントロール プレーンの操作に関する詳細が含まれています。 

```Kusto
CDBControlPlaneRequests
| where TimeGenerated > now(-6h)
| where  ApiKind == "Cassandra"
| where OperationName in ("Create", "Upsert", "Delete", "Execute")
| summarize by OperationName
 ```

## <a name="next-steps"></a>次の手順

- [Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md) についてさらに学習します。
- [ネイティブの Apache Cassandra から Azure Cosmos DB の Cassandra API に移行する](migrate-data-databricks.md)方法について確認します。