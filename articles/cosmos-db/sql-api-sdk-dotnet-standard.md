---
title: Azure Cosmos DB:SQL .NET Standard API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB .NET SDK の各バージョン間の変更など、SQL API と .NET SDK に関するあらゆる詳細を提供します。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 07/12/2019
ms.author: dech
ms.openlocfilehash: 0368e99135dd6e377dd2820b3e673c55182319b9
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663805"
---
# <a name="azure-cosmos-db-net-standard-sdk-for-sql-api-download-and-release-notes"></a>SQL API 用の Azure Cosmos DB .NET Standard SDK:ダウンロードおよびリリース ノート
> [!div class="op_single_selector"]
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
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
|**SDK のダウンロード**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**API ドキュメント**|[.NET API リファレンス ドキュメント](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**サンプル**|[.NET コード サンプル](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**作業開始**|[Azure Cosmos DB .NET SDK を開始する](sql-api-get-started.md)|
|**Web アプリ チュートリアル**|[Azure Cosmos DB を使用した Web アプリケーションの開発](sql-api-dotnet-application.md)|
|**現在サポートされているフレームワーク**|[Microsoft .NET Standard 2.0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>リリース ノート
### <a name="a-name310310"></a><a name="3.1.0"/>3.1.0
#### <a name="added"></a>追加済み
- [#541](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/541) クライアントおよびクエリ オプションに整合性レベルが追加されました
- [#544](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/544) LINQ に対する継続トークンのサポートが追加されました
- [#557](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/557) 項目要求オプションにトリガー オプションが追加されました
- [#571](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/571) オプションの設定を含む既定の JSON.net シリアライザーが追加されました
- [#572](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/572) CreateContainerIfNotExistsAsync にパーティション キーの検証が追加されました
- [#581](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/581) QueryDefinition API に LINQ が追加されました
- [#592](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/592) コンテナー ビルダーに CreateIfNotExistsAsync が追加されました
- [#597](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/597) ResponseMessage に継続トークン プロパティが追加されました
- [#604](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/604) LINQ ToStreamIterator 拡張メソッドが追加されました

#### <a name="fixed"></a>固定
- [#548](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/548) CosmosException.ToString() のメッセージの誤りを修正しました
- [#558](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/558) LocationCache ConcurrentDict のロックの競合を修正しました
- [#561](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/561) GetItemLinqQueryable が null クエリで動作するようになりました
- [#567](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/567) クエリで異なる言語カルチャが正しく処理されるようになりました
- [#574](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/574) 予期しない例外でクエリ解析が失敗した場合の空のエラー メッセージを修正しました
- [#576](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/576) クエリで入力がストリームに正しくシリアル化されるようになりました

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0 
* .NET SDK の[バージョン 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) の一般提供
* ターゲットは、.NET Framework 4.6.1 以降および .NET Core 2.0 以降をサポートする .NET Standard 2.0
* 最上位レベルの CosmosClient とメソッドが関連するデータベースとコンテナー クラスとの間で分割される、新しいオブジェクト モデル
* 高パフォーマンスの新しいストリーム API
* Change Feed プロセッサ API の組み込みサポート
* CosmosClient、コンテナー、Change Feed プロセッサ用の fluent ビルダー API
* 慣用的なスループット管理 API
* データベース、コンテナー、アイテム、クエリ、およびスループット要求用の詳細な RequestOptions と ResponseTypes
* 非パーティション分割コンテナーのスケーリング機能 
* 拡張可能でカスタマイズ可能なシリアライザー
* カスタム ハンドラーをサポートする拡張可能な要求パイプライン


## <a name="release--retirement-dates"></a>リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月**前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。 

提供終了になった SDK を使用した Azure Cosmos DB への要求は、サービスによって拒否されます。

<br/>

| Version | リリース日 | 提供終了日 |
| --- | --- | --- |
| [3.1.0](#3.1.0) |2019 年 7 月 29 日 |--- |
| [3.0.0](#3.0.0) |2019 年 7 月 15 日 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページをご覧ください。 

