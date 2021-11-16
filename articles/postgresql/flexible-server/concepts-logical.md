---
title: 論理レプリケーションと論理デコード - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure Database for PostgreSQL - フレキシブル サーバーでの論理レプリケーションと論理デコードの使用について説明します
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 13326622dae32c0ebd8fa86035967d51ab734c2a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241594"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーでの論理レプリケーションと論理デコード



Azure Database for PostgreSQL - フレキシブル サーバーでは、次の論理データの抽出とレプリケーションの手法がサポートされています。
1. **論理レプリケーション**
   1. データ オブジェクトをレプリケートする、PostgreSQL の[ネイティブ論理レプリケーション](https://www.postgresql.org/docs/12/logical-replication.html)を使用する。 論理レプリケーションを使用すると、テーブル レベルのデータ レプリケーションなど、データ レプリケーションをきめ細かく制御できます。
   2. 論理ストリーミング レプリケーションと、データベースの初期スキーマのコピー、TRUNCATE のサポート、DDL のレプリケート機能などの機能をさらに提供する [pglogical](https://github.com/2ndQuadrant/pglogical) 拡張機能を使用する。 
2. 先書きログ (WAL) の内容を [デコード](https://www.postgresql.org/docs/12/logicaldecoding-explanation.html)することで実施される **論理デコード**。 

## <a name="comparing-logical-replication-and-logical-decoding"></a>論理レプリケーションと論理デコードの比較
論理レプリケーションと論理デコードには、似ている点がいくつかあります。 両方とも:
* Postgres からデータをレプリケートできます。
* 変更のソースとして[先書きログ (WAL)](https://www.postgresql.org/docs/current/wal.html) を使用します。
* データを送信するために[論理レプリケーション スロット](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)を使用します。 スロットは、変更のストリームを表します。
* テーブルの [REPLICA IDENTITY プロパティ](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY)を使用して、送信できる変更を特定します。
* DDL の変更はレプリケートされません。


2 つのテクノロジには次のような違いがあります。論理レプリケーション: 
* レプリケートするテーブルまたはテーブルのセットを指定できます。
* PostgreSQL のインスタンス間でデータをレプリケートします。

論理デコード:
* データベース内のすべてのテーブルから変更を抽出します。
* PostgreSQL のインスタンス間でデータを直接送信することはできません。

>[!NOTE]
> 現在、フレキシブル サーバーではリージョンをまたがる読み取りレプリカはサポートされていません。 ワークロードの種類によっては、リージョンをまたがるディザスター リカバリー (DR) のために論理レプリケーション機能を使用することができます。

## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>論理レプリケーションと論理デコードの前提条件

1. ポータルのサーバー パラメーターのページに移動します。
2. サーバー パラメーター `wal_level` を `logical` に設定します。
3. pglogical 拡張機能を使用する場合は、`shared_preload_libaries` パラメーターを見つけて、ドロップダウン ボックスから `pglogical` を選択します。
4. `max_worker_processes` パラメーターの値を 16 以上に更新します。 そうしないと、`WARNING: out of background worker slots` などの問題が発生する可能性があります。
5. 変更を保存し、サーバーを再起動して `wal_level` の変更を適用します。
6. PostgreSQL のインスタンスで、接続しているリソースからのネットワーク トラフィックが許可されていることを確認します。
7. 管理者ユーザーのレプリケーションのアクセス許可を付与します。
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```
8. 使用しているロールに、レプリケートするスキーマに対する[特権](https://www.postgresql.org/docs/current/sql-grant.html)があることを確認する必要があります。 そうしないと、`Permission denied for schema` などのエラーが発生する可能性があります。 

## <a name="using-logical-replication-and-logical-decoding"></a>論理レプリケーションと論理デコードの使用

### <a name="native-logical-replication"></a>ネイティブ論理レプリケーション
論理レプリケーションでは、"パブリッシャー" と "サブスクライバー" という用語を使用します。 
* パブリッシャーは、データの送信 **元** である PostgreSQL データベースです。 
* サブスクライバーは、データの送信 **先** である PostgreSQL データベースです。

論理レプリケーションを試すために使用できるサンプル コードを次に示します。

1. パブリッシャー データベースに接続します。 テーブルを作成し、データをいくつか追加します。
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. テーブルのパブリケーションを作成します。
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. サブスクライバー データベースに接続します。 パブリッシャーと同じスキーマでテーブルを作成します。
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. 前に作成したパブリケーションに接続するサブスクリプションを作成します。
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. これで、サブスクライバーのテーブルに対してクエリを実行できるようになります。 パブリッシャーからデータを受信したことがわかります。
   ```SQL
   SELECT * FROM basic;
   ```
   パブリッシャーのテーブルに行を追加し、サブスクライバーで変更を表示することができます。

   データが見られない場合は、`azure_pg_admin` のログイン権限を有効にして表の内容を確認します。 
   ```SQL 
   ALTER ROLE azure_pg_admin login;
   ```


[論理レプリケーション](https://www.postgresql.org/docs/current/logical-replication.html)の詳細については、PostgreSQL のドキュメントを参照してください。

### <a name="pglogical-extension"></a>pglogical 拡張機能

プロバイダー データベース サーバーとサブスクライバーで pglogical を構成する例を次に示します。 詳しくは、[pglogical 拡張機能のドキュメント](https://github.com/2ndQuadrant/pglogical#usage)をご覧ください。 また、上で示した前提条件のタスクを実行してある必要があります。


1. プロバイダーとサブスクライバー両方のデータベース サーバーのデータベースに、pglogical 拡張機能をインストールします。
    ```SQL
   \C myDB
   CREATE EXTENSION pglogical;
   ```
2. レプリケーション ユーザーがサーバー管理ユーザー (サーバーを作成したユーザー) 以外の場合は、必ずユーザーに `azure_pg_admin` および `replication` 特権を割り当ててください。 または、管理者ユーザーをレプリケーション ユーザーに付与することもできます。 詳しくは、[pglogical のドキュメント](https://github.com/2ndQuadrant/pglogical#limitations-and-restrictions)をご覧ください。
   ```SQL
   GRANT azure_pg_admin, replication to myUser;
   ```
   または
   ```SQL
   GRANT myAdminUser to myUser;
   ```
2. **プロバイダー** (ソースとパブリッシャー) のデータベース サーバーで、プロバイダー ノードを作成します。
   ```SQL
   select pglogical.create_node( node_name := 'provider1', 
   dsn := ' host=myProviderServer.postgres.database.azure.com port=5432 dbname=myDB user=myUser password=myPassword');
   ```
3. レプリケーション セットを作成します。
   ```SQL
   select pglogical.create_replication_set('myreplicationset');
   ```
4. データベース内のすべてのテーブルをレプリケーション セットに追加します。
   ```SQL
   SELECT pglogical.replication_set_add_all_tables('myreplicationset', '{public}'::text[]);
   ```

   別の方法として、ou は特定のスキーマ (testUser など) から既定のレプリケーション セットにテーブルを追加することもできます。
   ```SQL
   SELECT pglogical.replication_set_add_all_tables('default', ARRAY['testUser']);
   ```

5. **サブスクライバー** のデータベース サーバーで、サブスクライバー ノードを作成します。
   ```SQL
   select pglogical.create_node( node_name := 'subscriber1', 
   dsn := ' host=mySubscriberServer.postgres.database.azure.com port=5432 dbname=myDB user=myUser password=myPasword' );
   ```
6. サブスクリプションを作成して、同期とレプリケーションのプロセスを開始します。
    ```SQL
   select pglogical.create_subscription (
   subscription_name := 'subscription1',
   replication_sets := array['myreplicationset'],
   provider_dsn := 'host=myProviderServer.postgres.database.azure.com port=5432 dbname=myDB user=myUser password=myPassword');
   ```
7. その後、サブスクリプションの状態を確認できます。
   ```SQL
   SELECT subscription_name, status FROM pglogical.show_subscription_status();
   ```

### <a name="logical-decoding"></a>論理デコード
論理デコードは、ストリーミング プロトコルまたは SQL インターフェイスを介して使用できます。 

#### <a name="streaming-protocol"></a>ストリーミング プロトコル
多くの場合、ストリーミング プロトコルを使用して変更を行うことをお勧めします。 独自のコンシューマーやコネクタを作成したり、[Debezium](https://debezium.io/) のようなサードパーティのサービスを使用したりできます。 

[pg_recvlogical によるストリーミング プロトコルの例](https://github.com/eulerto/wal2json#pg_recvlogical)については、wal2json のドキュメントを参照してください。

#### <a name="sql-interface"></a>SQL インターフェイス 
次の例では、SQL インターフェイスを wal2json プラグインと共に使用します。
 
1. スロットを作成します。
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. SQL コマンドを実行します。 次に例を示します。
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. 変更を使用します。
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   出力は以下のようになります。
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. 使用が完了したら、スロットをドロップします。
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```

[論理デコード](https://www.postgresql.org/docs/current/logicaldecoding.html)の詳細については、PostgreSQL のドキュメントを参照してください。


## <a name="monitoring"></a>監視
論理デコードは監視する必要があります。 未使用のレプリケーション スロットはすべてドロップしてください。 変更が読み取られるまで、スロットには Postgres WAL ログおよび関連するシステム カタログが保持されます。 サブスクライバーまたはコンシューマーが失敗した場合、または適切に構成されていない場合は、ログが消費されないまま蓄積され、ストレージがいっぱいになります。 また、未使用のログによって、トランザクション ID の折り返しリスクが増加します。 どちらの状況でも、サーバーを使用できなくなる可能性があります。 このため、論理レプリケーション スロットを継続的に使用することが重要になります。 論理レプリケーション スロットが使用されなくなった場合は、すぐにドロップしてください。

pg_replication_slots ビューの「active」列には、コンシューマーがスロットに接続されているかどうかが示されます。
```SQL
SELECT * FROM pg_replication_slots;
```
値が大きくなって通常のしきい値を超えたときに通知を受け取るには、フレキシブル サーバーのメトリック **Maximum Used Transaction IDs (使用されるトランザクション ID の最大数)** および **Storage Used (使用済みストレージ)** に [アラートを設定します](howto-alert-on-metrics.md)。 

## <a name="limitations"></a>制限事項
* [こちら](https://www.postgresql.org/docs/12/logical-replication-restrictions.html)に記載されているように、**論理レプリケーション** の制限が適用されます。
* **読み取りレプリカ** - Azure Database for PostgreSQL の読み取りレプリカは、現時点では、フレキシブル サーバーではサポートされていません。
* **スロットと HA フェールオーバー** - プライマリ サーバー上の論理レプリケーション スロットは、セカンダリ AZ 内のスタンバイ サーバーでは使用できません。 この状況は、サーバーでゾーン冗長高可用性オプションが使用されている場合に当てはまります。 スタンバイ サーバーへのフェールオーバーが発生した場合、論理レプリケーション スロットは、スタンバイ上で使用可能になりません。

## <a name="next-steps"></a>次のステップ
* [ネットワーク オプション](concepts-networking.md)の詳細を確認する
* フレキシブル サーバーで使用できる[拡張機能](concepts-extensions.md)について確認する
* [高可用性](concepts-high-availability.md)の詳細を確認する
