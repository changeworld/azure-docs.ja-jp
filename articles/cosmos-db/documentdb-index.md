---
title: "Azure Cosmos DB: DocumentDB API の記事 | Microsoft Docs"
description: "Azure Cosmos DB で DocumentDB API を使用したドキュメント データベース作成に固有の記事の一覧。"
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
ms.openlocfilehash: 8d618e00ed274f5d5705346a4ffd8281769de4e3
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/27/2017
---
# <a name="azure-cosmos-db-documentdb-api-documentation"></a>Azure Cosmos DB: DocumentDB API のドキュメント

この記事では、DocumentDB API に固有の、すべての Azure Cosmos DB コンテンツへのリンクを提供します。

これらの記事は、Graph API、テーブル API、または MongoDB API には適用されません。 

## <a name="introduction-and-concepts"></a>概要と概念

Azure Cosmos DB 用 DocumentDB API の学習を開始するにあたり、以下は必読のトピックとリソースです。

- [DocumentDB API の概要](documentdb-introduction.md)
- [データベース リソース モデル](documentdb-resources.md)
- Web サイト: [クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)
- チート シート: [SQL の文法](documentdb-sql-query-cheat-sheet.md)

## <a name="quickstarts"></a>クイック スタート

クイック スタートのトピックに従うと、Azure Cosmos DB で動作するアプリケーションをすばやく作成できます。 各クイック スタートで、UI ベースの Azure ポータルとお気に入りのコーディング言語を使って、Azure Cosmos DB でデータベース ソリューションを作成する方法について説明します。 GitHub の複製可能な Web アプリを各クイック スタートで利用できます。 

- [.NET + Azure ポータル + Web アプリ](create-documentdb-dotnet.md)
- [Java + Azure Portal + Web アプリ](create-documentdb-java.md)
- [Node.js + Azure Portal + Web アプリ](create-documentdb-nodejs.md)
- [Python + Azure Portal + Web アプリ](create-documentdb-python.md)
- [Xamarin + .NET + Azure Portal + Web アプリ](create-documentdb-xamarin-dotnet.md)

## <a name="tutorials"></a>Tutorials (チュートリアル)

チュートリアルには、クイック スタートよりも詳細な説明が記載されています。 こうしたチュートリアルでは、アプリをゼロから構築し、コードをコピーしてアプリに貼り付けます。 また、データのインポートや照会のほか、データベースをグローバルに配布する方法についても説明します。

### <a name="create-a-web-app"></a>Web アプリを作成する

- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md) 
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)

### <a name="create-a-console-app"></a>コンソール アプリを作成する

- [.NET](documentdb-get-started.md)
- [.NET Core](documentdb-dotnetcore-get-started.md) 
- [Java](documentdb-java-get-started.md) 
- [Node.JS](documentdb-nodejs-get-started.md) 
- [C++](documentdb-cpp-get-started.md)

### <a name="create-a-mobile-app"></a>モバイル アプリを作成する

- [Xamarin](mobile-apps-with-xamarin.md)

### <a name="work-with-data"></a>データの処理

- [データのインポート](import-data.md)
- [クエリ](tutorial-query-documentdb.md)
- [データのグローバル配布](tutorial-global-distribution-documentdb.md)

### <a name="work-with-azure-functions"></a>Azure Functions を操作する

- [Azure Cosmos DB のトリガーの作成](../azure-functions/functions-create-cosmos-db-triggered-function.md)
- [入力および出力バインドでの Azure Cosmos DB の使用](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="developers-guide"></a>開発者向けガイド

- [SQL クエリ](documentdb-sql-query.md)
- [パーティション分割](documentdb-partition-data.md)
- [サーバー側プログラミング: ストアド プロシージャ、データベース トリガー、UDF](programming.md)
- [パフォーマンス テスト](performance-testing.md)
- [パフォーマンスに関するヒント](performance-tips.md)
- [複数のマスターのセットアップ](multi-region-writers.md)
- [DateTime](working-with-dates.md)
- [ドキュメント データのモデリング](modeling-data.md) 

## <a name="sdks"></a>SDK

Azure Cosmos DB には、クライアント側でのアプリケーション開発を可能にする SDK が複数用意されています。

- [Java](documentdb-sdk-java.md)
- [.NET](documentdb-sdk-dotnet.md)
- [.NET Change Feed](documentdb-sdk-dotnet-changefeed.md)
- [.NET Core](documentdb-sdk-dotnet-core.md)
- [Node.JS](documentdb-sdk-node.md)
- [Python](documentdb-sdk-python.md)

## <a name="reference"></a>リファレンス

- [REST ()](/rest/api/documentdb/)
- [REST リソース プロバイダー](/rest/api/documentdbresourceprovider/)
- [SQL クエリのリファレンス](documentdb-sql-query-reference.md)
- [Azure Functions のリファレンス](../azure-functions/functions-bindings-documentdb.md)

## <a name="samples"></a>サンプル

サンプル ページには、一般的な DocumentDB API タスクのサンプル コードおよび API リファレンス コンテンツへのリンクがあります。

- [.NET](documentdb-dotnet-samples.md)
- [Node.js](documentdb-nodejs-samples.md)
- [Python](documentdb-python-samples.md) 
