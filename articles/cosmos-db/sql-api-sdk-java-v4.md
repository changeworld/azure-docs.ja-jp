---
title: SQL API 用 Azure Cosmos DB Java SDK v4 のリリース ノートとリソース
description: リリース日、提供終了日、Azure Cosmos DB SQL Async Java SDK の各バージョン間の変更など、SQL API 用 Azure Cosmos DB Java SDK v4 と SDK に関するあらゆる詳細を提供します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: b222a94ee754b24192261451d8ddc429886e705c
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725654"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Core (SQL) API 用 Azure Cosmos DB Java SDK v4: リリース ノートとリソース
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST リソース プロバイダー](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk executor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

Core (SQL) 用 Azure Cosmos DB Java SDK v4 では、非同期 API と同期 API が 1 つの Maven アーティファクトに結合されています。 v4 SDK は、Project Reactor と [Netty ライブラリ](https://netty.io/)に基づいて、強化されたパフォーマンス、新しい API 機能、非同期サポートを提供します。 ユーザーは、Azure Cosmos DB Java SDK v4 を使用することで、[Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) や [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md) よりもパフォーマンスを向上させることができます。

> [!IMPORTANT]  
> これらのリリース ノートは、Azure Cosmos DB Java SDK v4 のみを対象としています。 v4 より前のバージョンを現在使用している場合、v4 へのアップグレードについては、[Azure Cosmos DB Java SDK v4 への移行](migrate-java-v4-sdk.md)ガイドを参照してください。
>
> 次の 3 つの手順に従って作業を進めてください。
> 1. この SDK を使用できるように、[サポートされている最小 Java ランタイムの JDK 8](/java/azure/jdk/?view=azure-java-stable) をインストールします。
> 2. [Azure Cosmos DB Java SDK v4 のクイックスタート ガイド](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java)に従って、Maven アーティファクトにアクセスし、Azure Cosmos DB の基本的な要求の詳細を確認します。
> 3. Azure Cosmos DB Java SDK v4 の[パフォーマンスに関するヒント](performance-tips-java-sdk-v4-sql.md)と[トラブルシューティング](troubleshoot-java-sdk-v4-sql.md)のガイドを読んで、SDK をお使いのアプリケーションに最適化します。
>
> [Azure Cosmos DB のワークショップとラボ](https://aka.ms/cosmosworkshop)は、Azure Cosmos DB Java SDK v4 の使用方法を学習するための別の優れたリソースです。
>

| |  |
|---|---|
| **SDK のダウンロード** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API ドキュメント** | [Java API リファレンス ドキュメント](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1-beta.3/index.html) |
|**SDK への協力** | [GitHub の Azure SDK for Java 中央リポジトリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**開始するには** | [クイック スタート: Azure Cosmos DB SQL API データを管理する Java アプリを作成する](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [クイックスタート コードが含まれている GitHub リポジトリ](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**基本的なコード サンプル** | [Azure Cosmos DB:SQL API 用の Java のサンプル](sql-api-java-sdk-samples.md) · [サンプル コードが含まれている GitHub リポジトリ](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**変更フィードを使用するコンソール アプリ**| [変更フィード - Java SDK v4 のサンプル](create-sql-api-java-changefeed.md) · [サンプル コードが含まれている GitHub リポジトリ](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Web アプリのサンプル**| [Java SDK v4 を使用した Web アプリの構築](sql-api-java-application.md) · [サンプル コードが含まれている GitHub リポジトリ](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **パフォーマンスに関するヒント**| [Java SDK v4 のパフォーマンスに関するヒント](performance-tips-java-sdk-v4-sql.md)| 
| **トラブルシューティング** | [Java SDK v4 のトラブルシューティング](troubleshoot-java-sdk-v4-sql.md) |
| **以前の SDK から v4 への移行** | [Java V4 SDK への移行](migrate-java-v4-sdk.md) |
| **サポートされている最小ランタイム**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB のワークショップとラボ** |[Cosmos DB ワークショップのホーム ページ](https://aka.ms/cosmosworkshop)

