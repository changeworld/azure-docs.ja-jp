<properties
    pageTitle="Azure SQL Database Elastic Database クエリの概要"
    description="エラスティック クエリ機能の概要"
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="sidneyh"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/09/2015"
    ms.author="sidneyh" />

# Azure SQL Database Elastic Database クエリ (プレビュー) の概要

**Elastic Database クエリ機能** (プレビュー) を使用すると、Azure SQL Database の複数のデータベースにまたがる Transact-SQL クエリを実行することができます。特に共通のスキーマを共有する複数のデータベースがデータ層に含まれている場合、それらのデータ層に Microsoft ツールおよびサードパーティ ツール (Excel、PowerBI、Tableau など) を接続することができます (水平的パーティション分割またはシャーディングとも呼ばれる)。この機能により、クエリを SQL Database 内の大規模なデータ層にスケールアウトし、結果をビジネス インテリジェンス (BI) レポートで視覚化することができます。

エラスティック データベース クエリ アプリケーションを構築するには、「[Elastic Database クエリの概要](sql-database-elastic-query-getting-started.md)」を参照してください。

## エラスティック データベース クエリのシナリオ

Elastic Database クエリの目的は、複数のデータベースが 1 つの全体的な結果に行を提供するレポート シナリオを容易に実現することです。クエリは、ユーザーまたはアプリケーションによって直接、またはクエリ データベースに接続されているツールを使って間接的に構成できます。これは特に、商用の BI またはデータ統合ツール (または、変更することができないソフトウェア) を使用してレポートを作成する場合に便利です。エラスティック データベース クエリを使用すれば、Excel、PowerBI、Tableau、または Cognos などのツールの使い慣れた SQL Server 接続機能を使用して、複数のデータベースにまたがるクエリを容易に実行することができます。

また、SQL Server Management Studio や Visual Studio によって発行されるクエリ経由でデータベースのコレクション全体に簡単にアクセスでき、Entity Framework やその他の ORM 環境から複数のデータベースにまたがるクエリを容易に実行できます。図 1 に示すシナリオでは、既存のクラウド アプリケーション (Elastic Database ツール ライブラリを使用) はスケールアウトされたデータ層を基盤にしており、エラスティック データベース クエリを使用して複数のデータベースにまたがるレポートが作成されます。

**図 1**

![スケール アウトされたデータ層で使用されるエラスティック データベース クエリ][1]

データ層は、共通のスキーマを使用している多くのデータベースをまたいでスケール アウトされています。この方法は、水平的パーティション分割またはシャーディングとも呼ばれています。パーティション分割を実行および管理するには、(1) [Elastic Database クライアント ライブラリ](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)を使用するか、または (2) 複数のデータベースにデータを分散するアプリケーション固有のモデルを使用します。このトポロジの場合、レポートはしばしば複数のデータベースに範囲を広げる必要があります。エラスティック データベース クエリでは、単一の SQL データベースに接続できるようになったことで、リモート データベースからのクエリ結果は、単一の仮想データベースで生成されたかのように表示されます。


## Elastic Database クエリのトポロジ

エラスティック データベース クエリを使用して水平方向にパーティション分割されたデータ層に対してレポート タスクを実行するには、データ層のデータベースを表すエラスティック スケール シャード マップが必要です。一般に、このシナリオではシャード マップが 1 つだけ使用され、エラスティック データベース クエリ機能を備えた専用のデータベースがクエリをレポートするためのエントリ ポイントとして機能します。以下に説明するように、この専用のデータベースのみを、エラスティック データベース クエリ オブジェクトで構成する必要があります。図 2 に、このトポロジと、エラスティック データベース クエリ データベースおよびシャード マップを使用した構成を示します。

**注** 専用のエラスティック データベース クエリ データベースは SQL DB v12 データベースとする必要があります。最初は、Premium 層しかサポートされていません。シャード自体に対する制限はありません。

**図 2**

![Elastic Database クエリを使用してシャード化された層に対するレポートを作成する][2]

(**シャードレット** とは、シャード上のシャーディング キーの単一値に関連付けられているすべてのデータです。**シャーディング キー**とは、シャード間でデータを分散する方法を決定する値です。たとえば、リージョンごとに分散されるデータにはシャーディング キーとしてリージョン ID が割り当てられます。詳細については、「[エラスティック スケールの用語集](sql-database-elastic-scale-glossary.md)」を参照してください。


時間の経過と共に、Elastic Database クエリ機能によって追加のトポロジがサポートされるようになります。この記事は、新しい機能が使用できるようになると、それを反映するために更新されます。

## シャード マップを構成することによりエラスティック クエリを有効にする

エラスティック データベース クエリ ソリューションを作成するには、エラスティック データベース クエリに関係するリモート データベースを示すために、Elastic Database ツールの[**シャード マップ**](sql-database-elastic-scale-shard-map-management.md)が必要です。Elastic Database クライアント ライブラリをすでに使用している場合は、既存のシャード マップを使用できます。それ以外の場合は、Elastic Database ツールを使用してシャード マップを作成する必要があります。

次に示す C# コードの例では、シャード マップを作成し単一のリモート データベースをシャードとして追加する方法を説明します。

    ShardMapManagerFactory.CreateSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerCreateMode.ReplaceExisting, RetryBehavior.DefaultRetryBehavior);
    smm = ShardMapManagerFactory.GetSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerLoadPolicy.Lazy,
      RetryBehavior.DefaultRetryBehavior);
    map = smm.CreateRangeShardMap<int>("yourshardmapname");
    shard = map.CreateShard(new ShardLocation("yoursqldbserver", "yoursqldbdatabasename"));

シャード マップの詳細については、「[シャード マップの管理](sql-database-elastic-scale-shard-map-management.md)」を参照してください。

Elastic Database クエリ機能を使用するには、まずシャード マップを作成し、次にリモート データベースをシャードとして登録する必要があります。これは 1 回限りの操作です。シャード マップの変更は、リモート データベースの追加または削除 (既存のシャード マップに対する追加の操作) を行わない限り必要ありません。


## エラスティック データベース クエリのデータベース オブジェクトを作成する

エラスティック データベース クエリのエンドポイントからアクセスできるリモート テーブルを記述するために、アプリケーションおよびサードパーティ ツールにローカル テーブルであるかのように表示される外部テーブルを定義する機能を導入しています。これらのデータベース オブジェクトに対してクエリを送信するには、ツールを介してそれを暗黙的に行うか、または SQL Server Management Studio、Visual Studio Data Tools、またはアプリケーションから明示的に行います。エラスティック データベース クエリは、他の Transact-SQL ステートメントの場合と同様に実行されます。ただし、注目すべき相違点として、クエリでは、外部オブジェクトの基になっており多数存在する可能性のあるリモート データベースに処理を分散します。

Elastic Database クエリ機能は、以下の 4 つの DDL ステートメントに依存しています。通常、これらの DDL ステートメントは、アプリケーションのスキーマが変更された場合に 1 度使用するだけであり、ほとんど使用することはありません。

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
*    [CREATE CREDENTIAL](https://msdn.microsoft.com/library/ms189522.aspx)
*    [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
*    [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx)

### データベース スコープのマスター キーと資格情報

資格情報とは、エラスティック データベース クエリがエラスティック スケール シャード マップ、および Azure SQL DB のリモート データベースに接続するために使用するユーザー ID とパスワードを表します。必要なマスター キーと資格情報は、次の構文を使用して作成できます。

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';  
    CREATE CREDENTIAL <credential_name> ON DATABASE
    WITH IDENTITY = '<shard_map_username>',
    SECRET = '<shard_map_password>'
     [;]
&lt;shard_map_username> にサフィックス “@servername” が含まれていないことを確認してください。

マスター キーと資格情報は、次の構文を使用して削除できます。

    DROP CREDENTIAL <credential_name> ON DATABASE;
    DROP MASTER KEY;  

### 外部データ ソース

次の手順に従って、シャード マップを外部データ ソースとして登録する必要があります。外部データ ソースを作成および削除するための構文は次のように定義されています。

      CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
                     (TYPE = SHARD_MAP_MANAGER,
                     LOCATION = '<fully_qualified_server_name>',
                     DATABASE_NAME = '<shardmap_database_name>',
                     CREDENTIAL = <credential_name>,
                     SHARD_MAP_NAME = '<shardmapname>'
    ) [;]

次のステートメントを使用して、外部データ ソースを削除することができます。

    DROP EXTERNAL DATA SOURCE <data_source_name>[;]

ユーザーは、ALTER ANY EXTERNAL DATA SOURCE アクセス許可を所有している必要があります。このアクセス許可は、ALTER DATABASE アクセス許可に含まれています。

**例**

次の例では、外部データ ソースに対して CREATE ステートメントを使用する方法について説明します。

    CREATE EXTERNAL DATA SOURCE MyExtSrc
    WITH
    (
    TYPE=SHARD_MAP_MANAGER,
    LOCATION='myserver.database.windows.net',
    DATABASE_NAME='ShardMapDatabase',
    CREDENTIAL= SMMUser,
    SHARD_MAP_NAME='ShardMap'
    );

次のカタログ ビューから、現在の外部データ ソースの一覧を取得できます。

    select * from sys.external_data_sources;

シャード マップを読み取る場合、およびクエリの処理中にリモート データベース上のデータにアクセスする場合は、同じ資格情報を使用できます。


### 外部テーブル

Elastic Database クエリでは、Azure SQL DB 内の (複数の) リモート データベースにわたってパーティション分割されるテーブルを参照するように既存の外部テーブル構文を拡張します。前述の外部データ ソースの概念に基づいて、外部テーブルを作成および削除する構文を次のように定義します。

    CREATE EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name
        ( { <column_definition> } [ ,...n ])
        { WITH ( <sharded_external_table_options> ) }
    )[;]

    <sharded_external_table_options> ::=
          DATA_SOURCE = <External_Data_Source>,
          DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED | ROUND_ROBIN

シャーディング ポリシーでは、テーブルをシャード化されたテーブルとして扱うか、それともレプリケートされたテーブルとして扱うかを制御します。シャード化されたテーブルの場合、さまざまなシャードからのデータは重複しません。これに対して、レプリケートされたテーブルの場合、どのシャードのデータもすべて同じです。クエリ プロセッサは、この情報を頼りにして、正確でより効率的なクエリ処理を実現します。ラウンド ロビン分散は、そのテーブルのデータを分散するためにアプリケーション固有の方法が使用されることを意味します。

    DROP EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name[;]

**CREATE/DROP EXTERNAL TABLE** に対するアクセス許可: ALTER ANY EXTERNAL DATA SOURCE のアクセス許可が必要です。これは、基になるデータ ソースを参照する場合にも必要です。

**例**: 次の例では、外部テーブルの作成方法を示します。

    CREATE EXTERNAL TABLE [dbo].[order_line](
        [ol_o_id] [int] NOT NULL,
        [ol_d_id] [tinyint] NOT NULL,
        [ol_w_id] [int] NOT NULL,
        [ol_number] [tinyint] NOT NULL,
        [ol_i_id] [int] NOT NULL,
        [ol_delivery_d] [datetime] NOT NULL,
        [ol_amount] [smallmoney] NOT NULL,
        [ol_supply_w_id] [int] NOT NULL,
        [ol_quantity] [smallint] NOT NULL,
        [ol_dist_info] [char](24) NOT NULL
    )
    WITH
    (
        DATA_SOURCE = MyExtSrc,
        DISTRIBUTION=SHARDED(ol_w_id)
    );

次の例では、外部テーブルの一覧を現在のデータベースから取得する方法を示します。

    select * from sys.external_tables;


## レポート作成とクエリ実行

### クエリ
外部データ ソースと外部テーブルを定義すると、Elastic Database クエリ機能が有効になっているデータベースに、使い慣れた SQL データベース接続文字列を使用して接続することができます。これで、外部テーブルに対して完全な読み取り専用のクエリを実行できるようになりました。ただし、以下の[制限事項のセクション](#preview-limitations)で説明する制限があります。

**例**: 次のクエリでは、倉庫、注文、および注文明細行の間で 3 方向結合を実行し、いくつかの集計と選択的フィルターを使用します。倉庫、注文、および注文明細行が倉庫の ID 列でパーティション分割されていると仮定すると、エラスティック データベース クエリは、該当する結合をリモート データベースに併置し、クエリの高価な部分の処理をスケール アウトすることができます。

    select
        w_id as warehouse,
        o_c_id as customer,
        count(*) as cnt_orderline,
        max(ol_quantity) as max_quantity,
        avg(ol_amount) as avg_amount,
        min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id

### ストアド プロシージャ SP_ EXECUTE_FANOUT

SP_EXECUTE_FANOUT は、シャード マップで表されるデータベースにアクセスできるようにするストアド プロシージャです。ストアド プロシージャは、次のパラメーターを取ります。

-    **サーバー名** (nvarchar): シャード マップをホストする論理サーバーの完全な修飾名。
-    **シャード マップ データベース名** (nvarchar): シャード マップ データベースの名前。
-    **ユーザー名** (nvarchar): シャード マップ データベースとリモート データベースにログインするためのユーザー名。
-    **パスワード** (nvarchar): ユーザーのパスワード。
-    **シャード マップ名** (nvarchar): クエリで使用するシャード マップのる名前。
-    **クエリ**: 各シャードに実行されるクエリ。

このストアド プロシージャは、呼び出しパラメーターに入力されたシャード マップ情報を使用して、指定されたステートメントを、シャード マップに登録されているすべてのシャードで実行します。結果は、マルチシャード クエリによく似た UNION ALL セマンティクスを使用してマージされます。結果には、リモート データベース名の付いた「仮想」列も取り込まれます。

シャード マップ データベースに接続する場合も、シャードに接続する場合も同じ資格情報が使用されます。

**例**:

    sp_execute_fanout 'myserver.database.windows.net', N'ShardMapDb', N'myuser', N'MyPwd', N'ShardMap', N'select count(w_id) as foo from warehouse'

## ツールの接続性
Elastic Database クエリ データベースへの使い慣れた SQL DB 接続文字列を使用して、BI およびデータ統合ツールを接続することができます。ご使用のツールのデータ ソースとして SQL Server がサポートされていることを確認してください。Elastic Database クエリ データベースの外部オブジェクトを、実際のツールを使用して接続する他の SQL Server データベースの場合と同様に使用します。

## ベスト プラクティス
*    シャード マップ マネージャー データベースとシャード マップに定義されたデータベースとが Microsoft Azure からのアクセスをそれぞれのファイアウォール規則に従って許可することを確認します。これは、それらのデータベースに Elastic Database クエリ データベースが接続できるようにするために必要なことです。詳細については、「[Azure SQL DB ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx)」を参照してください。
*    エラスティック データベース クエリでは、外部テーブルで定義されたデータ分散を検証または適用しません。実際のデータ分散がテーブル定義に指定されたデータ分散と異なる場合、クエリが予期しない結果を生成する場合があります。
*    エラスティック データベース クエリは、計算の大部分をシャード上で実行できるクエリに最適です。通常、最適なクエリ パフォーマンスが得られるのは、シャード上で評価可能な選択的なフィルター述語を使用した場合、またはすべてのシャード上でパーティション分割方法により実行可能な、パーティション分割キーによる結合を使用した場合となります。その他のクエリ パターンでは、シャードからヘッド ノードに大量のデータを読み込む必要があり、パフォーマンスの低下が発生する可能性があります。

## コスト

Elastic Database クエリは、Azure SQL Database データベースのコストに含まれています。リモート データベースがエラスティック データベース クエリのエンドポイントとは異なるデータ センターに置かれていたとしても、トポロジはサポートされます。ただし、リモート データベースからのデータ送信は通常の Azure 送信料金で請求されます。

## プレビューの制限事項

プレビューに関しては、以下に示す点に留意してください。

*    Elastic Database クエリ機能は、最初は SQL DB v12 Premium パフォーマンス レベルでしか利用できません。ただし、エラスティック データベース クエリがアクセスするリモート データベースのレベルに制限はありません。
* 外部データ ソースによって参照される外部テーブルでは、リモート データベースに対する読み取り操作しかサポートしません。ただし、外部テーブルの定義自体が存在するエラスティック データベース クエリ データベースでは、Transact-SQL 機能を完全にポイントすることができます。これは、たとえば、SELECT column_list INTO local_table を使用して一時的な結果を保持する場合、または外部テーブルを参照するエラスティック データベース クエリ データベースでストアド プロシージャを定義する場合に便利です。
*    現在のところ、クエリ内のパラメーターをリモート データベースにプッシュすることはできません。パラメーター化されたクエリは、すべてのデータをヘッド ノードに表示する必要があるので、データのサイズによってはパフォーマンス低下の影響を受ける場合があります。一時的な回避策として、クエリ内でのパラメーターの使用を避けるか、または RECOMPILE オプションを使用してパラメーターを現在の値に自動的に置換します。
* 外部テーブルに対する列レベルの統計は、現在サポートされていません。
* シャーディング キーによる述語で特定のリモート データベースを処理から安全に除外できる場合、Elastic Database クエリでは、現在のところ、シャードの除去を実行しません。したがって、クエリは、クエリの外部データ ソースによって表されたすべてのリモート データベースに常にタッチします。
* さまざまなデータベースにあるテーブルを結合する必要とするクエリは、エラスティック データベース クエリ データベースに大量の行を返す場合があり、その結果としてパフォーマンスが低下します。各リモート データベースでローカルに処理可能なクエリを開発するか、または WHERE 句を使用して、結合を実行する前に各データベースからの関連する行を制限することをお勧めします。
*    エラスティック データベース クエリのメタデータ定義で使用される構文は、プレビュー期間中に変更されます。
*    SSMS または SSDT の Transact SQL スクリプト機能は、現在のところ、エラスティック データベース クエリ オブジェクトでは機能しません。

## フィードバック
体験に関するフィードバックは、Disqus または Stackoverflow で共有できるようになっています。サービスに関して何でもご意見とご感想をお寄せください (障害、機能差、悪口など)。

## 次のステップ
Elastic Database クエリの探索を開始するには、実際の動作を数分で実行できるステップ バイ ステップのチュートリアル ([Elastic Database クエリの概要](sql-database-elastic-query-getting-started.md)) をお試しください。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png

<!--anchors-->

<!---HONumber=July15_HO2-->