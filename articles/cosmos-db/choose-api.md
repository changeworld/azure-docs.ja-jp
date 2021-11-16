---
title: Azure Cosmos DB で API を選択する
description: ワークロードの要件に基づき、Azure Cosmos DB で SQL/Core、MongoDB、Cassandra、Gremlin、Table API から選択する方法について学習します。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/12/2021
ms.openlocfilehash: e120d4fa4d85ba2d52821460da1aa62eafd0455d
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158024"
---
# <a name="choose-an-api-in-azure-cosmos-db"></a>Azure Cosmos DB で API を選択する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB は、最新のアプリ開発に対応するフル マネージドの NoSQL データベースです。 Azure Cosmos DB では、管理、更新、パッチ適用が自動的に行われるため、ユーザー側でのデータベース管理が不要になります。 また、アプリケーションのニーズに対応して需要に容量を一致させるコスト効果の高いサーバーレスおよび自動スケーリング オプションにより、容量管理が処理されます。

## <a name="apis-in-azure-cosmos-db"></a>Azure Cosmos DB の API

Azure Cosmos DB では、Core (SQL) API、MongoDB 用 API、Cassandra API、Gremlin API、Table API を含む、複数のデータベース API が提供されます。 これらの API を使用することで、ドキュメント、キー値、グラフ、および列ファミリのデータ モデルを使って実世界のデータをモデル化できます。 これらの API を使用すると、アプリケーションでは、他のさまざまなデータベース テクノロジと同様に Azure Cosmos DB を扱うことができ、管理のオーバーヘッドやスケーリング アプローチが不要になります。 これらの API を使用すれば、Azure Cosmos DB が、データのモデル化とクエリに既に存在するエコシステム、ツール、スキルを利用するのに役立ちます。

すべての API により、ストレージとスループットの自動スケーリング、柔軟性、およびパフォーマンスの保証が提供されます。 最良の API は 1 つではなく、API のいずれかを選択してアプリケーションをビルドすることができます。 この記事は、ワークロードとチームの要件に基づいて API を選択するのに役立ちます。

## <a name="considerations-when-choosing-an-api"></a>API 選択時の考慮事項

Core(SQL) API は Azure Cosmos DB にネイティブなものです。

MongoDB、Cassandra、Gremlin、Table 用の API では、オープンソース データベース エンジンのワイヤ プロトコルが実装されます。 これらの API は、次の条件が満たされている場合に最適です。

* 既存の MongoDB、Cassandra、または Gremlin アプリケーションがある場合。
* データ アクセス層全体を書き直す必要がない場合。
* オープンソースの開発者エコシステム、クライアント ドライバー、専門知識、データベースのリソースを使用する場合。
* グローバル配布、ストレージとスループットのエラスティック スケーリング、パフォーマンス、低遅延、トランザクションおよび分析ワークロードの実行機能など、Azure Cosmos DB の主な機能を利用し、フル マネージド プラットフォームを使用する場合。
* マルチクラウド環境で最新のアプリを開発している場合。

これらの API を使用して新しいアプリケーションをビルドしたり、既存のデータを移行したりできます。 移行したアプリを実行するには、アプリケーションの接続文字列を変更し、以前と同じように引き続き実行します。 既存のアプリを移行するときは、必ずこれらの API の機能サポートを評価してください。

ワークロードに基づいて、要件に合った API を選択する必要があります。 次の図は、新しいアプリをビルドするとき、または既存のアプリを Azure Cosmos DB に移行するときに適切な API を選択する方法に関するフロー チャートを示しています。

:::image type="content" source="./media/choose-api/choose-api-decision-tree.png" alt-text="Azure Cosmos DB で API を選択するためのデシジョン ツリー。" lightbox="./media/choose-api/choose-api-decision-tree.png":::

## <a name="coresql-api"></a>Core(SQL) API

この API では、データをドキュメント形式で格納します。 インターフェイス、サービス、SDK クライアント ライブラリを完全に制御できるため、最適なエンドツーエンド エクスペリエンスが提供されます。 Azure Cosmos DB にロールアウトされた新機能は、SQL API アカウントで最初に使用できるようになります。 Azure Cosmos DB SQL API アカウントでは、構造化照会言語 (SQL) 構文を使用した項目のクエリがサポートされています。これは、JSON オブジェクトを照会するための最もよく知られている一般的なクエリ言語の 1 つです。 詳細については、[Azure Cosmos DB SQL API](/learn/modules/intro-to-azure-cosmos-db-core-api/) の学習モジュールおよび「[SQL クエリの使用を開始する](sql-query-getting-started.md)」を参照してください。

Oracle、DynamoDB、HBase などの他のデータベースから移行する場合や、最新のテクノロジを使用してアプリをビルドする場合は、SQL API を使用することをお勧めします。 SQL API では分析がサポートされ、運用と分析のワークロード間でパフォーマンスを分離できます。

### <a name="capacity-planning-for-migration-to-api-for-sqlcoreapi"></a>SQL (コア) API への移行のための容量計画

既存のデータベース クラスターから Azure Cosmos DB SQL API への移行のための容量計画を行おうとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
* 既存のシャード化およびレプリケートされたデータベース クラスター内の仮想コアとサーバーの数のみがわかっている場合は、[仮想コアまたは vCPU を使用した要求ユニットの見積もり](convert-vcore-to-request-unit.md)に関するページを参照してください。
* 現在のデータベース ワークロードの標準的な要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-with-capacity-planner.md)に関するページを参照してください。

## <a name="api-for-mongodb"></a>MongoDB 用 API

この API では、BSON 形式を使用してドキュメント構造にデータを格納します。 MongoDB ワイヤ プロトコルと互換性がありますが、ネイティブ MongoDB に関するコードは使用されません。 この API は、スケーリング、高可用性、geo レプリケーション、複数の書き込み場所、自動および透過的なシャード管理、運用および分析ストア間の透過的なレプリケーションなどの Azure Cosmos DB の機能の使用を損なうことなく、より広範な MongoDB エコシステムとスキルを使用する場合に最適です。

接続文字列を変更するだけで、MongoDB 用 API で既存の MongoDB アプリを使用できます。 mongodump と mongorestore などのネイティブ MongoDB ツールまたは Azure Database Migration ツールを使用して、既存のデータを移動できます。 MongoDB シェル、[MongoDB Compass](mongodb/connect-using-compass.md)、[Robo3T](mongodb/connect-using-robomongo.md) などのツールを使用すると、クエリを実行し、ネイティブ MongoDB の場合と同様にデータを操作できます。

MongoDB 用 API は、MongoDB サーバー バージョン 4.0、3.6、3.2 と互換性があります。 最高のパフォーマンスと全機能のサポートが提供されるため、サーバー バージョン 4.0 をお勧めします。 詳細については、[MongoDB 用 API](mongodb/mongodb-introduction.md) に関する記事を参照してください。

### <a name="capacity-planning-for-migration-to-api-for-mongodb"></a>MongoDB 用 API への移行のための容量計画

既存のデータベース クラスターから Azure Cosmos DB MongoDB 用 API への移行のための容量計画を行おうとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
* 既存のデータベース クラスター内の仮想コアとサーバーの数のみがわかっている場合は、[仮想コアまたは vCPU を使用した要求ユニットの見積もり](convert-vcore-to-request-unit.md)に関するページを参照してください。
* 現在のデータベース ワークロードの標準的な要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](./mongodb/estimate-ru-capacity-planner.md)に関するページを参照してください。

## <a name="cassandra-api"></a>Cassandra API

この API では、列指向スキーマにデータを格納します。 Apache Cassandra では、列指向スキーマに対する柔軟なアプローチを提供しながら、大量のデータを格納するための高度な分散型水平方向スケーリング アプローチを提供します。 Azure Cosmos DB の Cassandra API は、分散 NoSQL データベースに取り組むこの哲学と一致します。 Cassandra API は、Apache Cassandra と互換性があるワイヤ プロトコルです。 Azure Cosmos DB の弾力性とフル マネージドの性質を活用しながら、ほとんどのネイティブ Apache Cassandra 機能、ツール、およびエコシステムを使用する場合は、Cassandra API を考慮する必要があります。 これは、Cassandra API では、OS、Java VM、ガベージ コレクター、読み取り/書き込みパフォーマンス、ノード、クラスターなどを管理する必要がないことを意味します。

Apache Cassandra クライアント ドライバーを使用して、Cassandra API に接続することができます。 Cassandra API を使用すると、使い慣れた Cassandra クエリ言語 (CQL)、CQL シェルなどのツール、Cassandra クライアント ドライバーを使ってデータを操作できます。 現在、Cassandra API でサポートされているのは OLTP シナリオのみです。 Cassandra API を使えば、変更フィードなどの Azure Cosmos DB に固有の機能を使用することもできます。 詳細については、[Cassandra API](cassandra-introduction.md) に関する記事をご覧ください。

## <a name="gremlin-api"></a>Gremlin API

この API を使用すると、ユーザーはグラフ クエリを作成でき、データはエッジと頂点として格納されます。 この API は、動的データ、複雑な関係を持つデータ、リレーショナル データベースでモデル化するには複雑すぎるデータに関するシナリオ、および既存の Gremlin エコシステムとスキルを利用する場合に使用します。 Azure Cosmos DB の Gremlin API では、グラフ データベース アルゴリズムの機能を非常にスケーラブルなマネージド インフラストラクチャと結合します。 柔軟性の不足に関連する最も一般的なデータの問題への一意で柔軟な解決策と、リレーショナル アプローチを提供します。 現在、Gremlin API でサポートされているのは OLTP シナリオのみです。

Azure Cosmos DB の Gremlin API は、[Apache TinkerPop](https://tinkerpop.apache.org/) グラフ コンピューティング フレームワークに基づいています。 Gremlin API では、同じグラフ クエリ言語を使用してデータの取り込みとクエリを実行します。 また、Azure Cosmos DB のパーティション戦略を使用して、グラフ データベース エンジンからの読み取り/書き込み操作を行います。 Gremlin API では、オープンソースの Gremlin によるワイヤ プロトコル サポートが得られるため、オープンソースの Gremlin SDK を使用してアプリケーションをビルドできます。 Azure Cosmos DB の Gremlin API は、複雑な分析グラフ シナリオ用の Apache Spark および [GraphFrames](https://github.com/graphframes/graphframes) でも使用できます。 詳細については、[Gremlin API](graph-introduction.md) に関する記事を参照してください。

## <a name="table-api"></a>テーブル API

この API では、データをキー/値形式で格納します。 現在、Azure Table Storage を使用している場合は、待機時間、スケーリング、スループット、グローバル配布、インデックス管理、低クエリ パフォーマンスにいくつかの制限がある可能性があります。 Table API によってこれらの制限が克服されます。Azure Cosmos DB のベネフィットを活用する場合は、アプリを移行することをお勧めします。 Table API でサポートされているのは、OLTP シナリオのみです。

Azure Table Storage 用に作成されたアプリケーションを Table API に移行することができます。これにより、コードにほとんど変更を加えることなく、高度な機能を利用できるようになります。 詳細については、[Table API](table/introduction.md) に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB SQL API を使ってみる](create-sql-api-dotnet.md)
* [Azure Cosmos DB の MongoDB 用 API の概要](mongodb/create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API を使ってみる](cassandra/manage-data-dotnet.md)
* [Azure Cosmos DB Gremlin API を使ってみる](create-graph-dotnet.md)
* [Azure Cosmos DB Table API を使ってみる](create-table-dotnet.md)
* Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
    * 既存のデータベース クラスター内の仮想コアとサーバーの数のみがわかっている場合は、[仮想コア数または仮想 CPU 数を使用した要求ユニットの見積もり](convert-vcore-to-request-unit.md)に関するページを参照してください 
    * 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-with-capacity-planner.md)に関するページを参照してください
