---
title: "Azure Cosmos DB: SQL API の記事 | Microsoft Docs"
description: "Azure Cosmos DB で SQL API を使用したドキュメント データベース作成に固有の記事の一覧。"
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 82bec99a-ac2b-474e-b41f-d2fb296c8feb
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: mimig
ms.openlocfilehash: 8de2a541f307adfed1125dbfc25825b3e28d9c48
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-sql-api-documentation"></a>Azure Cosmos DB: SQL API のドキュメント

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

この記事では、SQL API に固有の、すべての Azure Cosmos DB コンテンツへのリンクを提供します。

これらの記事は、Graph API、テーブル API、または MongoDB API には適用されません。 

## <a name="introduction-and-concepts"></a>概要と概念

Azure Cosmos DB の SQL API の学習を開始するにあたり、次のトピックとリソースは必ず読む必要があります。

- [SQL API の概要](sql-api-introduction.md)
- [データベース リソース モデル](sql-api-resources.md)
- Web サイト: [クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)
- チート シート: [SQL の文法](sql-api-sql-query-cheat-sheet.md)

## <a name="quickstarts"></a>クイック スタート

クイック スタートのトピックに従うと、Azure Cosmos DB で動作するアプリケーションをすばやく作成できます。 各クイック スタートで、UI ベースの Azure ポータルとお気に入りのコーディング言語を使って、Azure Cosmos DB でデータベース ソリューションを作成する方法について説明します。 GitHub の複製可能な Web アプリを各クイック スタートで利用できます。 

- [.NET + Azure ポータル + Web アプリ](create-sql-api-dotnet.md)
- [Java + Azure Portal + Web アプリ](create-sql-api-java.md)
- [Node.js + Azure Portal + Web アプリ](create-sql-api-nodejs.md)
- [Python + Azure Portal + Web アプリ](create-sql-api-python.md)
- [Xamarin + .NET + Azure Portal + Web アプリ](create-sql-api-xamarin-dotnet.md)

## <a name="tutorials"></a>チュートリアル

チュートリアルには、クイック スタートよりも詳細な説明が記載されています。 こうしたチュートリアルでは、アプリをゼロから構築し、コードをコピーしてアプリに貼り付けます。 また、データのインポートや照会のほか、データベースをグローバルに配布する方法についても説明します。

### <a name="create-a-web-app"></a>Web アプリを作成する

- [.NET](sql-api-dotnet-application.md)
- [Node.js](sql-api-nodejs-application.md) 
- [Java](sql-api-java-application.md)
- [Python](sql-api-python-application.md)

### <a name="create-a-console-app"></a>コンソール アプリを作成する

- [.NET](sql-api-get-started.md)
- [.NET Core](sql-api-dotnetcore-get-started.md) 
- [Java](sql-api-java-get-started.md) 
- [Node.JS](sql-api-nodejs-get-started.md) 
- [C++](sql-api-cpp-get-started.md)

### <a name="create-a-mobile-app"></a>モバイル アプリを作成する

- [Xamarin](mobile-apps-with-xamarin.md)

### <a name="work-with-data"></a>データの処理

- [データのインポート](import-data.md)
- [クエリ](tutorial-query-sql-api.md)
- [データのグローバル配布](tutorial-global-distribution-sql-api.md)

### <a name="work-with-azure-functions"></a>Azure Functions を操作する

- [Azure Cosmos DB のトリガーの作成](../azure-functions/functions-create-cosmos-db-triggered-function.md)
- [入力および出力バインドでの Azure Cosmos DB の使用](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="developers-guide"></a>開発者向けガイド

- [SQL クエリ](sql-api-sql-query.md)
- [パーティション分割](sql-api-partition-data.md)
- [サーバー側プログラミング: ストアド プロシージャ、データベース トリガー、UDF](programming.md)
- [パフォーマンス テスト](performance-testing.md)
- [パフォーマンスに関するヒント](performance-tips.md)
- [複数のマスターのセットアップ](multi-region-writers.md)
- [DateTime](working-with-dates.md)
- [ドキュメント データのモデリング](modeling-data.md) 

## <a name="sdks"></a>SDK

Azure Cosmos DB には、クライアント側でのアプリケーション開発を可能にする SDK が複数用意されています。

- [Java](sql-api-sdk-java.md)
- [.NET](sql-api-sdk-dotnet.md)
- [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
- [.NET Core](sql-api-sdk-dotnet-core.md)
- [Node.js](sql-api-sdk-node.md)
- [Python](sql-api-sdk-python.md)

## <a name="reference"></a>リファレンス

- [REST](/rest/api/documentdb/)
- [REST リソース プロバイダー](/rest/api/documentdbresourceprovider/)
- [SQL クエリのリファレンス](sql-api-sql-query-reference.md)
- [Azure Functions のリファレンス](../azure-functions/functions-bindings-documentdb.md)

## <a name="samples"></a>サンプル

サンプル ページには、一般的な SQL API タスクのサンプル コードおよび API リファレンス コンテンツへのリンクがあります。

- [.NET](sql-api-dotnet-samples.md)
- [Node.js](sql-api-nodejs-samples.md)
- [Python](sql-api-python-samples.md) 
