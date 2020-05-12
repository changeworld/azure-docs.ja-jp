---
title: Azure Cosmos DB:Bulk Executor Java API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB Bulk Executor Java SDK の各バージョン間の変更など、Bulk Executor Java API と SDK に関するあらゆる詳細を提供します。
author: milismsft
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/21/2018
ms.author: adrianmi
ms.openlocfilehash: 0030f974a36dc80dc8c4112000aa5934126a2482
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836446"
---
# <a name="java-bulk-executor-library-download-information"></a>Java Bulk Executor ライブラリ:ダウンロード情報

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST リソース プロバイダー](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk executor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**説明**|Bulk Executor ライブラリを使うと、クライアント アプリケーションは、Azure Cosmos DB アカウントで一括操作を実行できます。 Bulk Executor ライブラリは、BulkImport および BulkUpdate 名前空間を提供します。 BulkImport モジュールは、コレクションに対してプロビジョニングされているスループットを最大限まで消費するように最適化された方法で、ドキュメントを一括して取り込むことができます。 BulkUpdate モジュールでは、Azure Cosmos コンテナー内の既存のデータをパッチとして一括更新できます。|
|**SDK のダウンロード**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**GitHub の Bulk Executor ライブラリ**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API ドキュメント**| [Java API リファレンス ドキュメント](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**開始するには**|[Bulk Executor ライブラリ Java SDK の概要](bulk-executor-java.md)|
|**サポートされている最小ランタイム**|[Java Development Kit (JDK) 7+](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>リリース ノート

### <a name="2100"></a><a name="2.10.0"/>2.10.0

* JSON から入れ子になったパーティション キー値を正しく抽出するように DocumentAnalyzer.java を修正します。

### <a name="294"></a><a name="2.9.4"/>2.9.4

* 特定のエラーに対して再試行する機能を BulkDelete 操作に追加し、再試行可能なエラーの一覧をユーザーに返します。

### <a name="293"></a><a name="2.9.3"/>2.9.3

* Cosmos SDK バージョン 2.4.7 の更新プログラム。

### <a name="292"></a><a name="2.9.2"/>2.9.2

* 'id' とパーティション キーの値の後に配置されているすべての修正済みドキュメント プロパティが更新された項目の一覧に追加されるように、'mergeAll' が 'id' とパーティション キーの値で続行するように修正します。

### <a name="291"></a><a name="2.9.1"/>2.9.1

* コンカレンシーの開始次数を 1 に更新し、minibatch のデバッグ ログを追加します。


