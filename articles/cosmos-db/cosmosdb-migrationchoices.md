---
title: Cosmos DB の移行オプション
description: このドキュメントでは、オンプレミスまたはクラウドのデータを Azure Cosmos DB に移行するためのさまざまなオプションについて説明します
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/03/2021
ms.openlocfilehash: 412b047896496124b042de82d9841afba9112934
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562772"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>オンプレミスまたはクラウドのデータを Azure Cosmos DB に移行するためのオプション
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

さまざまなデータ ソースから Azure Cosmos DB にデータを読み込むことができます。 Azure Cosmos DB は複数の API をサポートしているため、ターゲットには既存のあらゆる API を指定できます。 Azure Cosmos DB にデータを移行する場合のいくつかのシナリオを次に示します。

* ある Azure Cosmos コンテナーから、同じデータベースまたは別のデータベース内の別のコンテナーにデータを移動する。
* 専用コンテナーから共有データベース コンテナーにデータを移動する。
* region1 にある Azure Cosmos アカウントから、同じまたは別のリージョンの別の Azure Cosmos アカウントにデータを移動する。
* Azure Blob Storage、JSON ファイル、Oracle Database、Couchbase、DynamoDB などのソースから Azure Cosmos DB にデータを移動する。

さまざまなソースからさまざまな Azure Cosmos DB API への移行パスをサポートする目的で、移行パスごとに特別な処理を行うソリューションが複数用意されています。 このドキュメントでは、利用できるソリューションをリストアップし、その利点と制限事項について説明します。

## <a name="factors-affecting-the-choice-of-migration-tool"></a>移行ツールの選択に影響を与える要因

移行ツールの選択は次の要因によって決定されます。

* **オンライン移行かオフライン移行:** 移行ツールの多くで、1 回限りの移行を行うためのパスが与えられます。 これは、データベースにアクセスしているアプリケーションに一定時間のダウンタイムが発生する可能性を意味します。 一部の移行ソリューションでは、ライブ移行の手段が与えられます。ライブ移行では、移行元と移行先の間にレプリケーション パイプラインが設定されます。

* **データ ソース**:既存のデータは、Oracle DB2、Datastax Cassandra、Azure SQL Database、PostgreSQL など、さまざまなデータ ソースに含まれています。データは既存の Azure Cosmos DB アカウントに含まれていることもあります。データ モデルを変更することや、パーティション キーの異なるコンテナーに含まれるデータを再パーティションすることが移行の意図になることもあります。

* **Azure Cosmos DB API**:Azure Cosmos DB の SQL API に対しては、さまざまな移行シナリオを支援するさまざまなツールが Azure Cosmos DB チームによって開発されています。 他の API にはすべて、コミュニティによって開発され、保守されている独自の特別なツール セットが含まれています。 Azure Cosmos DB ではそのような API がワイヤ プロトコル レベルでサポートされているため、Azure Cosmos DB にデータを移行する間、それらのツールもそのまま動作するはずです。 ただし、この概念は Azure Cosmos DB に固有であるため、スロットルのカスタム処理が必要になることがあります。

* **データのサイズ**:データセットが小規模の場合、ほとんどの移行ツールは非常に快適に動作します。 データセットが数百ギガ バイトを超えると、移行ツールの選択肢が限られます。 

* **予想される移行時間**:移行は消費するスループットの少ない、ゆっくりしたインクリメンタル ペースで行うように構成するか、移行先 Azure Cosmos DB コンテナーでプロビジョニングされているスループット全体を消費し、少ない時間で移行を完了するように構成できます。

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API

容量計画に関するサポートが必要な場合は、[Azure Cosmos DB キャパシティ プランナーで RU/s を見積もる方法に関するガイド](estimate-ru-with-capacity-planner.md)を読むことを検討してください。 
* 仮想コアまたはサーバー ベースのプラットフォームから移行するのに、要求ユニットを見積もる方法のガイドが必要な場合は、[仮想コアから RU/s を見積もる方法に関するガイド](estimate-ru-with-capacity-planner.md)を読むことを検討してください。

|移行の種類|解決策|サポートされているソース|サポート対象|考慮事項|
|---------|---------|---------|---------|---------|
|オフライン|[データ移行ツール](import-data.md)| &bull;JSON/CSV ファイル<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;AWS DynamoDB<br/>&bull;Azure Blob Storage|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB Tables API<br/>&bull;JSON ファイル |&bull; セットアップが簡単で、さまざまなソースをサポートします。 <br/>&bull; 大規模なデータセットには適していません。|
|オフライン|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON/CSV ファイル<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB 用 Azure Cosmos DB API<br/>&bull;MongoDB <br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure Blob Storage <br/> <br/>サポートされているその他のソースについては、[Azure Data Factory](../data-factory/connector-overview.md) に関する記事をご覧ください。|&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB 用 Azure Cosmos DB API<br/>&bull;JSON ファイル <br/><br/> サポートされているその他のターゲットについては、[Azure Data Factory](../data-factory/connector-overview.md) に関する記事をご覧ください。 |&bull; セットアップが簡単で、さまざまなソースをサポートします。<br/>&bull; Azure Cosmos DB Bulk Executor ライブラリを使用します。 <br/>&bull; 大規模なデータセットに適しています。 <br/>&bull; チェックポイントがありません - つまり、移行の途中で問題が発生した場合、移行プロセスを全部やり直す必要があります。<br/>&bull; 配信不能キューがありません - つまり、エラーが含まれるファイルがいくつかあると、移行プロセス全体が停止することがあります|
|オフライン|[Azure Cosmos DB Spark コネクタ](./create-sql-api-spark.md)|Azure Cosmos DB SQL API。 <br/><br/>Spark エコシステムからの追加コネクタで他のソースを使用できます。| Azure Cosmos DB SQL API。 <br/><br/>Spark エコシステムからの追加コネクタで他のターゲットを使用できます。| &bull; Azure Cosmos DB Bulk Executor ライブラリを使用します。 <br/>&bull; 大規模なデータセットに適しています。 <br/>&bull; カスタム Spark セットアップが必要です。 <br/>&bull; Spark ではスキーマの不整合に注意を要し、移行中、問題になることがあります。 |
|オフライン|[カスタム ツールと Cosmos DB Bulk Executor ライブラリ](migrate-cosmosdb-data.md)| ソースはカスタム コードに依存します | Azure Cosmos DB SQL API| &bull; 移行の回復性を向上させるチェックポイント機能や配信不能機能を提供します。 <br/>&bull; 非常に大規模なデータセット (10 TB 以上) に適しています。  <br/>&bull; App Service として実行するこのツールのカスタム セットアップが必要になります。 |
|オンライン|[Cosmos DB Functions + ChangeFeed API](change-feed-functions.md)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; セットアップが簡単です。 <br/>&bull; ソースが Azure Cosmos DB コンテナーの場合にのみ動作します。 <br/>&bull; 大規模なデータセットには適していません。 <br/>&bull; ソース コンテナーからの削除をキャプチャすることはありません。 |
|オンライン|[ChangeFeed を利用したカスタム移行サービス](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; 進捗状況を追跡します。 <br/>&bull; ソースが Azure Cosmos DB コンテナーの場合にのみ動作します。 <br/>&bull; 大規模なデータセットでも機能します。<br/>&bull; ChangeFeed プロセッサをホストする目的で App Service を設定することがユーザーに求められます。 <br/>&bull; ソース コンテナーからの削除をキャプチャすることはありません。|
|オンライン|[Striim](cosmosdb-sql-api-migrate-data-striim.md)| &bull;Oracle <br/>&bull;Apache Cassandra<br/><br/> サポートされているその他のソースについては、[Striim の Web サイト](https://www.striim.com/sources-and-targets/)を参照してください。 |&bull;Azure Cosmos DB SQL API <br/>&bull; Azure Cosmos DB の Cassandra API<br/><br/> サポートされているその他のターゲットについては、[Striim の Web サイト](https://www.striim.com/sources-and-targets/)を参照してください。 | &bull; Oracle、DB2、SQL Server などのさまざまなソースで動作します。<br/>&bull; ETL パイプラインの構築が簡単で、監視用のダッシュボードを提供します。 <br/>&bull; 大規模なデータセットをサポートします。 <br/>&bull; これはサードパーティ製のツールであるため、マーケットプレースから購入し、ユーザーの環境にインストールする必要があります。|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API

[移行前ガイド](mongodb/pre-migration-steps.md)の説明に従って、移行の計画を立てます。 
* 容量計画に関するサポートが必要な場合は、[Azure Cosmos DB キャパシティ プランナーで RU/s を見積もる方法に関するガイド](estimate-ru-with-capacity-planner.md)を読むことを検討してください。 
* 仮想コアまたはサーバー ベースのプラットフォームから移行するのに、要求ユニットを見積もる方法のガイドが必要な場合は、[仮想コアから RU/s を見積もる方法に関するガイド](convert-vcore-to-request-unit.md)を読むことを検討してください。

移行準備を終えて移行ツールを使用するときは、下に挙げる詳細なガイドを利用できます
* [MongoDB ネイティブ ツールによるオフライン移行](mongodb/tutorial-mongotools-cosmos-db.md)
* [Azure Database Migration Service (DMS) を使用したオフライン移行](../dms/tutorial-mongodb-cosmos-db.md)
* [Azure Database Migration Service (DMS) を使用したオンライン移行](../dms/tutorial-mongodb-cosmos-db-online.md)
* [Azure Databricks と Spark によるオフライン/オンライン移行](mongodb/migrate-databricks.md)

移行を終えたら、[移行後ガイド](mongodb/post-migration-optimization.md)の説明に従って Azure Cosmos DB のデータ資産を最適化します。

現在のソリューションから Azure Cosmos DB API for MongoDB への移行様式を下にまとめます。

|移行の種類|解決策|サポートされているソース|サポート対象|考慮事項|
|---------|---------|---------|---------|---------|
|オンライン|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB|MongoDB 用 Azure Cosmos DB API |&bull; Azure Cosmos DB Bulk Executor ライブラリを使用します。 <br/>&bull; 大規模なデータセットに適し、ライブ変更のレプリケーションを処理します。 <br/>&bull; 他の MongoDB ソースでのみ機能します。|
|オフライン|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB| MongoDB 用 Azure Cosmos DB API| &bull; Azure Cosmos DB Bulk Executor ライブラリを使用します。 <br/>&bull; 大規模なデータセットに適し、ライブ変更のレプリケーションを処理します。 <br/>&bull; 他の MongoDB ソースでのみ機能します。|
|オフライン|[Azure Data Factory](../data-factory/connector-azure-cosmos-db-mongodb-api.md)| &bull;JSON/CSV ファイル<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB 用 Azure Cosmos DB API <br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure Blob Storage <br/><br/> サポートされているその他のソースについては、[Azure Data Factory](../data-factory/connector-overview.md) に関する記事をご覧ください。 | &bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB 用 Azure Cosmos DB API <br/>&bull; JSON ファイル <br/><br/> サポートされているその他のターゲットについては、[Azure Data Factory](../data-factory/connector-overview.md) に関する記事をご覧ください。| &bull; セットアップが簡単で、さまざまなソースをサポートします。 <br/>&bull; Azure Cosmos DB Bulk Executor ライブラリを使用します。 <br/>&bull; 大規模なデータセットに適しています。 <br/>&bull; チェックポイントがなく、移行の途中で問題が発生した場合、移行プロセスを全部やり直す必要があります。<br/>&bull; 配信不能キューがなく、エラーが含まれるファイルがいくつかあっただけで移行プロセス全体が停止することがあります。 <br/>&bull; 特定のデータ ソースの読み取りスループットを向上させるためのカスタム コードが必要です。|
|オフライン|既存の Mongo ツール ([mongodump](mongodb/tutorial-mongotools-cosmos-db.md#mongodumpmongorestore)、[mongorestore](mongodb/tutorial-mongotools-cosmos-db.md#mongodumpmongorestore)、[Studio3T](mongodb/connect-using-mongochef.md))|MongoDB | MongoDB 用 Azure Cosmos DB API| &bull; セットアップと統合が簡単です。 <br/>&bull; スロットルのカスタム処理が必要です。|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB の Cassandra API

容量計画に関するサポートが必要な場合は、[Azure Cosmos DB キャパシティ プランナーで RU/s を見積もる方法に関するガイド](estimate-ru-with-capacity-planner.md)を読むことを検討してください。 

|移行の種類|解決策|サポートされているソース|サポート対象|考慮事項|
|---------|---------|---------|---------|---------|
|オフライン|[cqlsh COPY コマンド](cassandra/migrate-data.md#migrate-data-by-using-the-cqlsh-copy-command)|CSV ファイル | Azure Cosmos DB の Cassandra API| &bull; セットアップが簡単です。 <br/>&bull; 大規模なデータセットには適していません。 <br/>&bull; ソースが Cassandra テーブルの場合にのみ機能します。|
|オフライン|[Spark でテーブルをコピーする](cassandra/migrate-data.md#migrate-data-by-using-spark) | &bull;Apache Cassandra<br/>&bull;Azure Cosmos DB の Cassandra API| Azure Cosmos DB の Cassandra API | &bull; Spark 機能を使用し、変換と取り込みを並列処理できます。 <br/>&bull; スロットルを処理するカスタム再試行ポリシーを含む構成が必要です。|
|オンライン|[Striim (Oracle DB/Apache Cassandra からの)](cassandra/migrate-data-striim.md)| &bull;Oracle<br/>&bull;Apache Cassandra<br/><br/> サポートされているその他のソースについては、[Striim の Web サイト](https://www.striim.com/sources-and-targets/)を参照してください。|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB の Cassandra API <br/><br/> サポートされているその他のターゲットについては、[Striim の Web サイト](https://www.striim.com/sources-and-targets/)を参照してください。| &bull; Oracle、DB2、SQL Server などのさまざまなソースで動作します。 <br/>&bull; ETL パイプラインの構築が簡単で、監視用のダッシュボードを提供します。 <br/>&bull; 大規模なデータセットをサポートします。 <br/>&bull; これはサードパーティ製のツールであるため、マーケットプレースから購入し、ユーザーの環境にインストールする必要があります。|
|オンライン|[Blitzz (Oracle DB/Apache Cassandra からの)](cassandra/oracle-migrate-cosmos-db-blitzz.md)|&bull;Oracle<br/>&bull;Apache Cassandra<br/><br/>サポートされているその他のソースについては、[Blitzz の Web サイト](https://www.blitzz.io/)を参照してください。 |Azure Cosmos DB の Cassandra API。 <br/><br/>サポートされているその他のターゲットについては、[Blitzz の Web サイト](https://www.blitzz.io/)を参照してください。 | &bull; 大規模なデータセットをサポートします。 <br/>&bull; これはサードパーティ製のツールであるため、マーケットプレースから購入し、ユーザーの環境にインストールする必要があります。|

## <a name="other-apis"></a>その他の API

SQL API、Mongo API、Cassandra API 以外の API については、API の既存エコシステム別にさまざまなツールがサポートされています。 

**テーブル API** 

* [データ移行ツール](table/table-import.md#data-migration-tool)
* [AzCopy](table/table-import.md#migrate-data-by-using-azcopy)

**Gremlin API**

* [Graph Bulk Executor ライブラリ](bulk-executor-graph-dotnet.md)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>次のステップ

* Azure Cosmos DB への移行のための容量計画を実行しようとしていますか?
    * 知っていることが既存のデータベース クラスター内の仮想コアとサーバーの数のみである場合は、[仮想コアまたは仮想 CPU の数を使用した要求ユニットの見積もり](convert-vcore-to-request-unit.md)に関するページを参照してください 
    * 現在のデータベース ワークロードの通常の要求量が分かる場合は、[Azure Cosmos DB キャパシティ プランナーを使用して要求ユニットを見積もる方法](estimate-ru-with-capacity-planner.md)に関する記事を読みます
* [.NET](bulk-executor-dot-net.md) と [Java](bulk-executor-java.md) で Bulk Executor ライブラリを使用するサンプル アプリケーションを試して、さらに詳しく学習します。 
* Bulk Executor ライブラリは Cosmos DB Spark コネクタに統合されています。詳細については、[Azure Cosmos DB Spark コネクタ](./create-sql-api-spark.md)に関する記事をご覧ください。  
* 大規模な移行に関して別途支援が必要な場合は、問題のタイプに "General Advisory (一般的な勧告)" を、問題のサブタイプに "Large (TB+) migrations (大規模な (TB 以上の) 移行)" を選択してサポート チケットを開き、Azure Cosmos DB 製品チームに連絡します。