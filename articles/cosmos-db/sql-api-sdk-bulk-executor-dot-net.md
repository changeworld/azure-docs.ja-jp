---
title: 'Azure Cosmos DB: Bulk Executor .NET API、SDK、およびリソース | Microsoft Docs'
description: リリース日、提供終了日、Azure Cosmos DB Bulk Executor .NET SDK の各バージョン間の変更など、Bulk Executor .NET API と SDK に関するあらゆる詳細を提供します。
services: cosmos-db
author: tknandu
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/14/2018
ms.author: ramkris
ms.openlocfilehash: ffd8f438429cd8769ac0dbff7f489327166e0000
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294460"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET Bulk Executor ライブラリ: ダウンロード情報 

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
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [Bulk Executor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**説明**</td><td>Bulk Executor ライブラリを使うと、クライアント アプリケーションは、Azure Cosmos DB アカウントで一括操作を実行できます。 Bulk Executor ライブラリは、BulkImport、BulkUpdate、および BulkDelete 名前空間を提供します。 BulkImport モジュールは、コレクションに対してプロビジョニングされているスループットを最大限まで消費するように最適化された方法で、ドキュメントを一括して取り込むことができます。 BulkUpdate モジュールは、Azure Cosmos DB コンテナー内の既存のデータを、パッチとして一括更新できます。 BulkDelete モジュールは、コレクションに対してプロビジョニングされているスループットを最大限まで消費するように最適化された方法で、ドキュメントを一括削除できます。</td></tr>

<tr><td>**SDK のダウンロード**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/)</td></tr>

<tr><td>**GitHub の BulkExecutor ライブラリ**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)</td></tr>

<tr><td>**API ドキュメント**</td><td>[.NET API リファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)</td></tr>

<tr><td>**はじめに**</td><td>[Bulk Executor ライブラリ .NET SDK の概要](bulk-executor-dot-net.md)</td></tr>

<tr><td>**現在サポートされているフレームワーク**</td><td><ul><li>[Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (バージョン 2.0.0 以降)</li><li>
[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (バージョン 9.0.1 以降)
</li></ul></td></tr>
</table></br>

## <a name="release-notes"></a>リリース ノート

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Azure Cosmos DB SQL API アカウントの BulkDelete 操作のサポートが追加されました。
* Azure Cosmos DB MongoDB API アカウントの BulkImport 操作のサポートが追加されました。
* DocumentDB .NET SDK の依存関係がバージョン 2.0.0 に引き上げられました。 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Azure Cosmos DB Gremlin API アカウントの BulkImport 操作のサポートが追加されました。

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Azure Cosmos DB SQL API アカウントの BulkImport 操作の小さなバグ修正。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Azure Cosmos DB SQL API アカウントの BulkImport 操作および BulkUpdate 操作のサポートが追加されました。
