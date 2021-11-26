---
title: Azure Cosmos DB:SQL Async Java API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB SQL Async Java SDK の各バージョン間の変更など、SQL Async Java API と SDK に関するあらゆる詳細を提供します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/11/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 209fc857296556124cb5fa2003fb0c578dfe3317
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397568"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>SQL API 用 Azure Cosmos DB Async Java SDK:リリース ノートとリソース
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
> * [REST](/rest/api
> * [REST リソース プロバイダー](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-query-getting-started.md)
> * [Bulk Executor - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

SQL API Async Java SDK は、[Netty ライブラリ](https://netty.io/)をサポートした非同期操作が可能であるという点で、SQL API Java SDK とは異なります。 既存の [SQL API Java SDK](sql-api-sdk-java.md) では、非同期操作はサポートされません。 

> [!IMPORTANT]  
> これは Azure Cosmos DB 用の最新の Java SDK では "*ありません*"。 プロジェクトには [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) を使用することを検討してください。 アップグレードするには、[Azure Cosmos DB Java SDK v4 への移行](migrate-java-v4-sdk.md)に関するガイド、および[ Reactor と RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) に関するガイドの手順に従ってください。 
>

> [!IMPORTANT]  
> 2024 年 8 月 31 日に、Azure Cosmos DB Async Java SDK v2.x は廃止されます。この SDK と、SDK を使用しているすべてのアプリケーションは、**引き続き機能します**。Azure Cosmos DB は、この SDK の保守とサポートをさらに提供することを停止します。
> 上記の手順に従って Azure Cosmos DB Java SDK v4 に移行することをお勧めします。
>

| | リンク |
|---|---|
| **SDK のダウンロード** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API ドキュメント** |[Java API リファレンス ドキュメント](/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient) | 
|**SDK への協力** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**開始するには** | [Async Java SDK の開始](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Code sample** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **パフォーマンスに関するヒント**| [GitHub の Readme](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **サポートされている最小ランタイム**|[JDK 8](/java/azure/jdk/) | 

## <a name="release-history"></a>リリース履歴

リリース履歴は Azure Cosmos DB Java SDK ソース リポジトリで保守管理されています。 機能リリースと各リリースで修正されたバグの詳細な一覧については、[SDK 変更ログのドキュメント](https://github.com/Azure/azure-cosmosdb-java/blob/master/changelog/README.md)を参照してください

## <a name="faq"></a>よく寄せられる質問
[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページを参照してください。