---
title: Azure Cosmos DB の Azure Cosmos DB 内の変更フィードへのアクセス
description: この記事では、Azure Cosmos DB の Azure Cosmos DB 内の変更フィードの読み取りとアクセスに使用できるさまざまなオプションについて説明します。
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688130"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Azure Cosmos DB の変更フィードの読み取り

次のいずれかのオプションを使用して、Azure Cosmos DB の変更フィードを操作することができます。

* Azure Functions の使用
* 変更フィード プロセッサ ライブラリの使用
* Azure Cosmos DB SQL API SDK を使用する

## <a name="using-azure-functions"></a>Azure Functions の使用

Azure Functions は、最も簡単で推奨されるオプションです。 Cosmos DB 用の Azure Functions トリガーを作成するときに、接続先のコンテナーを選択でき、コンテナーに変更が加えられるたびに Azure Function がトリガーされます。 トリガーを作成するには、Azure Functions ポータル、Azure Cosmos DB ポータル、または SDK を使用したプログラムを使用します。 Visual Studio と VS Code では、Azure Functions の作成がサポートされ、クロスプラットフォーム開発のために Azure Functions CLI を使用することもできます。 デスクトップでコードを記述してデバッグを行い、1 回のクリック操作で関数をデプロイできます。 詳しくは、[Azure Functions を使用したサーバーレス データベース コンピューティング](serverless-computing-database.md)および[変更フィードと Azure Functions の使用](change-feed-functions.md)に関する記事をご覧ください。

## <a name="using-the-change-feed-processor-library"></a>変更フィード プロセッサ ライブラリの使用

変更フィード プロセッサライブラリでは、複雑さが隠されますが、依然として変更フィードを完全に制御できます。 このライブラリはオブザーバー パターンに従い、処理関数はライブラリによって呼び出されます。 高スループットの変更フィードがあれば、変更フィードを読み取るための複数のクライアントをインスタンス化できます。 変更フィード プロセッサ ライブラリを使用しているので、負荷は異なるクライアント間に自動的に分割されます。このロジックを実装する必要はありません。 複雑さはすべてライブラリによって処理されます。 独自のロード バランサーを使用する場合は、カスタム パーティション戦略用の `IPartitionLoadBalancingStrategy` を実装して変更フィードを処理できます。 詳しくは、[変更フィード プロセッサ ライブラリの使用](change-feed-processor.md)に関する記事をご覧ください。

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Azure Cosmos DB SQL API SDK を使用する

SDK を使用すると、変更フィードを低レベルで制御できます。 チェックポイントの管理、特定の論理パーティション キーへのアクセスなどを行うことができます。複数の閲覧者がいる場合、`ChangeFeedOptions` を使用して、読み取りの負荷を異なるスレッドや異なるクライアントに分散させることができます。

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Cassandra と MongoDB の API の変更フィード

変更フィード機能は、MongoDB API では変更ストリームとして表示され、Cassandra API では述語を含むクエリとして表示されます。 MongoDB API の実装の詳細については、[MongoDB 用の Azure Cosmos DB API の変更ストリーム](mongodb-change-streams.md)に関するページを参照してください。

ネイティブ Apache Cassandra には、変更データ キャプチャ (CDC) が用意されています。CDC は、特定のテーブルに対してアーカイブのフラグを設定し、CDC ログ用に構成可能なディスク上のサイズに達すると、そのテーブルへの書き込みを拒否するメカニズムです。 Azure Cosmos DB API for Cassandra の変更フィード機能により、CQL を介して述語を使用して変更をクエリする機能が向上します。 実装の詳細については、[Cassandra 用の Azure Cosmos DB API の変更フィード](cassandra-change-feed.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

以下の記事で、変更フィードに関してさらに詳しく知ることができます。

* [変更フィードの概要](change-feed.md)
* [変更フィードと Azure Functions の併用](change-feed-functions.md)
* [Change Feed Processor ライブラリの使用](change-feed-processor.md)
