---
title: Azure Cosmos DB:SQL .NET API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB .NET SDK の各バージョン間の変更など、SQL .NET API と SDK に関するあらゆる詳細を提供します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/11/2021
ms.author: anfeldma
ms.custom: devx-track-dotnet
ms.openlocfilehash: a52d3e5690a043e3f886dd0a87db5ffba155528f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493364"
---
# <a name="azure-cosmos-db-net-sdk-v2-for-sql-api-download-and-release-notes-deprecated"></a>Azure Cosmos DB .NET SDK v2 for SQL API: ダウンロードおよびリリース ノート (非推奨)
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

| | リンク |
|---|---|
|**SDK のダウンロード**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**API ドキュメント**|[.NET API リファレンス ドキュメント](/dotnet/api/overview/azure/cosmosdb)|
|**サンプル**|[.NET コード サンプル](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples)|
|**開始するには**|[Azure Cosmos DB .NET SDK を開始する](sql-api-get-started.md)|
|**Web アプリ チュートリアル**|[Azure Cosmos DB を使用した Web アプリケーションの開発](sql-api-dotnet-application.md)|
|**現在サポートされているフレームワーク**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

> [!IMPORTANT]
> 2024 年 8 月 31 日に、Azure Cosmos DB .NET SDK v2.x は廃止されます。この SDK と、SDK を使用しているすべてのアプリケーションは、引き続き機能します。Azure Cosmos DB は、この SDK の保守とサポートをさらに提供することを停止します。 これらの手順に従って、.NET SDK v3 SDK の最新バージョンに移行することをお勧めします。
>

> [!NOTE]
> .NET Framework を使用している場合は、.NET Standard をターゲットとする [.NET SDK](sql-api-sdk-dotnet-standard.md) の最新バージョン 3.x を参照してください。

## <a name="release-history"></a><a name="release-history"></a> リリース履歴

リリース履歴は Azure Cosmos DB .NET SDK ソース リポジトリで保守管理されています。 機能リリースと各リリースで修正されたバグの詳細な一覧については、[SDK 変更ログのドキュメント](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)を参照してください

Azure Cosmos DB .NET SDK のバージョン 3 には更新された機能とパフォーマンスの向上が含まれるため、この SDK の 2.x は 2024 年 8 月 31 日に廃止される予定です。その日付までに SDK をバージョン 3 に更新する必要があります。 手順に従って Azure Cosmos DB .NET SDK バージョン 3 に移行することが推奨されます。

## <a name="recommended-version"></a><a name="recommended-version"></a> 推奨されるバージョン

.NET SDK のさまざまなサブ バージョンは 2.x.x バージョン以下で利用できます。 **推奨される最小バージョンは 2.16.2 です**。

## <a name="known-issues"></a><a name="known-issues"></a> 既知の問題

[推奨される最小バージョン](#recommended-version)に影響を与える既知の問題の一覧を次に示します。

| 問題 | 影響 | 対応策 | 追跡リンク |
| --- | --- | --- | --- |
| 複数の書き込み場所があるアカウントで直接モードを使用している場合、リージョンがアカウントに追加されたときに、SDK によって検出されないことがあります。 [アカウント情報を更新する](troubleshoot-sdk-availability.md#adding-a-region-to-an-account)バックグラウンド プロセスが起動に失敗します。 |現在のリージョンよりも上位の順番で PreferredLocations の一部である新しいリージョンがアカウントに追加された場合、SDK によって新しい利用可能なリージョンが検出されません。 |アプリケーションを再起動します |https://github.com/Azure/azure-cosmos-dotnet-v2/issues/852 |

## <a name="faq"></a>よく寄せられる質問

[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目

Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページを参照してください。
