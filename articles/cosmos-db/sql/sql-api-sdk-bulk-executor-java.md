---
title: Azure Cosmos DB:Bulk Executor Java API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB Bulk Executor Java SDK の各バージョン間の変更など、Bulk Executor Java API と SDK に関するあらゆる詳細を提供します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 9798c7cd870bc05901e47cc1bcf410bd6d5a42a6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114067"
---
# <a name="java-bulk-executor-library-download-information"></a>Java Bulk Executor ライブラリ:ダウンロード情報
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP コネクタ](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP コネクタ](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST リソース プロバイダー](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-query-getting-started.md)
> * [Bulk Executor - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

| | リンク/メモ |
|---|---|
|**説明**|Bulk Executor ライブラリを使うと、クライアント アプリケーションは、Azure Cosmos DB アカウントで一括操作を実行できます。 Bulk Executor ライブラリは、BulkImport および BulkUpdate 名前空間を提供します。 BulkImport モジュールは、コレクションに対してプロビジョニングされているスループットを最大限まで消費するように最適化された方法で、ドキュメントを一括して取り込むことができます。 BulkUpdate モジュールでは、Azure Cosmos コンテナー内の既存のデータをパッチとして一括更新できます。|
|**SDK のダウンロード**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**GitHub の Bulk Executor ライブラリ**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API ドキュメント**| [Java API リファレンス ドキュメント](/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**開始するには**|[Bulk Executor ライブラリ Java SDK の概要](bulk-executor-java.md)|
|**サポートされている最小ランタイム**|[Java Development Kit (JDK) 7+](/java/azure/jdk/)|

## <a name="release-notes"></a>リリース ノート
### <a name="2123"></a><a name="2.12.3"></a>2.12.3

* `GoneException` が `IllegalStateException` にラップされている場合の再試行ポリシーを修正します - この変更は、Gateway キャッシュが 410 上で確実に更新されるようにして、Spark コネクタ (Spark 2.4 の場合) がカスタム再試行ポリシーを使用して、パーティション分割中にクエリを成功させるために必要です

### <a name="2122"></a><a name="2.12.2"></a>2.12.2

* 一時的なエラーが発生しても、ドキュメントが常にインポートされるわけではない問題を修正しました。

### <a name="2121"></a><a name="2.12.1"></a>2.12.1

* 最新の Cosmos Core SDK バージョンを使用するようにアップグレードしてください。

### <a name="2120"></a><a name="2.12.0"></a>2.12.0

* Bulk 操作のために Spark コネクタを介して提供される RU の予算の処理を改善します。 最初の1回限りの一括インポートは、baseBatchSize を使用した Spark コネクタから実行され、上記の一括インポートの RU 消費量が収集されます。
  MiniBatchSizeAdjustmentFactor は、上記の RU 消費量に基づいて計算され、ミニバッチ サイズはこれに基づいて調整されます。 各一括インポートにおける経過時間と使用された RU に基づいて、スリープ時間は 1 秒あたりの RU 消費量を制限するために計算され、次の一括インポートの前にスレッドを一時停止するために使用されます。

### <a name="2110"></a><a name="2.11.0"></a>2.11.0

* 入れ子になったパーティション キーを使用する場合の一括更新を防止するバグを修正します

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* JSON から入れ子になったパーティション キー値を正しく抽出するように DocumentAnalyzer.java を修正します。

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* 特定のエラーに対して再試行する機能を BulkDelete 操作に追加し、再試行可能なエラーの一覧をユーザーに返します。

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Cosmos SDK バージョン 2.4.7 の更新プログラム。

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* 'id' とパーティション キーの値の後に配置されているすべての修正済みドキュメント プロパティが更新された項目の一覧に追加されるように、'mergeAll' が 'id' とパーティション キーの値で続行するように修正します。

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* コンカレンシーの開始次数を 1 に更新し、minibatch のデバッグ ログを追加します。