<properties
    pageTitle="シャーディング (行方向のパーティション分割) のためのエラスティック データベース クエリ | Microsoft Azure"
    description="行方向のパーティションでエラスティック クエリを設定する方法"    
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
    ms.date="10/15/2015"
    ms.author="sidneyh;torsteng" />

# シャーディング (行方向のパーティション分割) のためのエラスティック データベース クエリ

このドキュメントでは、行方向のパーティション分割シナリオ用にエラスティック クエリを設定する方法と、クエリを実行する方法について説明します。行方向のパーティション分割シナリオの定義については、[エラスティック データベース クエリの概要 (プレビュー)](sql-database-elastic-query-overview.md) に関するページを参照してください。

この機能は、Azure SQL [Database Elastic Database 機能セット](sql-database-elastic-scale.md)の一部です。
 
## データベース オブジェクトの作成

エラスティック データベース クエリは、T-SQL 構文を拡張して、シャーディング (行方向のパーティション分割) を使用して多数のデータベースにデータを分散するデータ層を参照します。ここでは、シャーディングされたテーブルに対するエラスティック クエリに関係する DDL ステートメントの概要を説明します。これらのステートメントを使うと、エラスティック クエリ データベース内のシャーディングされたデータのメタデータ表現を作成できます。これらのステートメントを実行するための前提条件は、エラスティック データベース クライアント ライブラリを使用してシャード マップを作成することです。詳細については、「[シャード マップの管理](sql-database-elastic-scale-shard-map-management.md)」を参照してください。または、「[Elastic Database ツールの概要](sql-database-elastic-scale-get-started.md)」のサンプルを使ってシャード マップを作成してください。

エラスティック データベース クエリのデータベース オブジェクトの定義は、次の T-SQL ステートメントに依存します。以降では、行方向のパーティション分割シナリオ向けにこれらの T-SQL ステートメントについて説明します。

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) 

* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)

* [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)

* [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx)

### 1\.1 データベース スコープのマスター キーと資格情報 

資格情報とは、エラスティック クエリが Azure SQL DB のリモート データベースに接続するために使用するユーザー ID とパスワードを表します。必要なマスター キーと資格情報を作成するには、次の構文を使用します。

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = ’password’;
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = ‘<username>’,  
    SECRET = ‘<password>’
    [;]

資格情報とキーを削除するには、次の構文を使用します。

    DROP DATABASE SCOPED CREDENTIAL <credential_name>;  
    DROP MASTER KEY;   

 
**注**: *< username>* にサフィックス *"@servername"* が含まれていないことを確認してください。

### 1\.2 外部データ ソース

外部データ ソースを定義して、シャード マップとデータ層に関する情報を渡します。外部データ ソースは、シャード マップを参照します。エラスティック クエリは、外部データ ソースと基になるシャード マップを使用して、データ層にあるデータベースを列挙します。外部データ ソースを作成するための構文を次に示します。

	<External_Data_Source> ::=    
	CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                               	           
			(TYPE = SHARD_MAP_MANAGER,
                   	LOCATION = '<fully_qualified_server_name>',
			DATABASE_NAME = ‘<shardmap_database_name>',
			CREDENTIAL = <credential_name>, 
			SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 
 
外部データ ソースを削除するための構文を次に示します。

	DROP EXTERNAL DATA SOURCE <data_source_name>[;] 

#### CREATE/DROP EXTERNAL DATA SOURCE のアクセス許可 

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

シャード マップを読み取る場合と、エラスティック クエリの処理中にシャード上のデータにアクセスする場合は、同じ資格情報を使用できます。

### 1\.3 外部テーブル 
 
エラスティック クエリでは、外部テーブル DDL を拡張して、複数のデータ ソースにわたって行方向にパーティション分割された外部テーブルを参照します。外部テーブルの定義は、次の側面に対応しています。

* **スキーマ**: 外部テーブル DDL は、クエリで使用できるスキーマを定義します。外部テーブル定義に指定するスキーマは、実際のデータが格納されているシャード上のテーブルのスキーマに一致する必要があります。 

* **データの分散**: 外部テーブル DDL は、データ層の間でデータを分散するためのデータの分散を定義します。Azure SQL Database では、外部テーブルに定義した分散はシャード上の実際の分散に対して検証されないことに注意してください。ユーザー側で、シャード上の実際のデータの分散が外部テーブルでの定義に一致することを確認する必要があります。

* **データ層参照**: 外部テーブル DDL は、外部データ ソースを参照します。外部データ ソースは、データ層のすべてのデータベースを見つけるために必要な情報を外部テーブルに提供するシャード マップを指定します。

前のセクションで説明したように外部データ ソースを使用する、外部テーブルを作成および削除するための構文を次に示します。

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
	    { WITH ( <sharded_external_table_options> ) }
	) [;]  
	
	<sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
	  [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

DATA\_SOURCE 句は、外部テーブルに使用される外部データ ソース (つまり、行方向のパーティション分割の場合はシャード マップ) を定義します。

SCHEMA\_NAME 句と OBJECT\_NAME 句は、外部テーブル定義をシャード上の別のスキーマのテーブルまたは別の名前を持つテーブルにマップする機能をそれぞれ提供します。これらを省略した場合、リモート オブジェクトのスキーマは "dbo" と見なされ、その名前は定義されている外部テーブルの名前と同一であると見なされます。

SCHEMA\_NAME 句と OBJECT\_NAME 句は、外部テーブルを作成するデータベースにリモート テーブルの名前が既に取得されている場合に特に便利です。この問題の例として、スケールアウトされたデータ層のカタログ ビューまたは DMV の集計ビューを取得する外部テーブルを定義する場合が挙げられます。カタログ ビューと DMV は既にローカルに存在するため、外部テーブルの定義にその名前を使うことはできません。代わりに、別の名前を使用して、カタログ ビューまたは DMV の名前を SCHEMA\_NAME 句または OBJECT\_NAME 句で使用します (次の例をご覧ください)。

DISTRIBUTION 句は、このテーブルに使用するデータ分散を指定します。

* SHARDED は、シャード マップ内のデータベース間でこのテーブルのデータが行方向にパーティション分割されることを意味します。データ分散のパーティション分割キーは、<sharding_column_name> パラメーターを介して渡されます。  

* REPLICATED は、テーブルの同一のコピーがシャード マップ内の各データベースに存在することを意味します。Azure SQL DB は、テーブルのコピーを保持しません。データベース間でレプリカが同じであることを自分で確認する必要があります。

* ROUND\_ROBIN は、行方向のパーティション分割を使用してテーブルが分散されることを意味します。ただし、アプリケーションに依存する分散が使用されています。

クエリ プロセッサは、DISTRIBUTION 句で提供される情報を使用して、最も効率的なクエリ プランを作成します。

次のステートメントを使用して、外部テーブルを削除することができます。

	DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]  

**CREATE/DROP EXTERNAL TABLE に対するアクセス許可**: ALTER ANY EXTERNAL DATA SOURCE のアクセス許可が必要です。これは、基になるデータ ソースを参照する場合にも必要です。

**セキュリティに関する考慮事項**: 外部テーブルへのアクセス権を持つユーザーは、外部データ ソース定義に指定された資格情報の下で、基になるリモート テーブルへのアクセス権を自動的に取得します。外部データ ソースの資格情報を介した特権の不要な昇格を回避するために、外部テーブルへのアクセスを慎重に管理する必要があります。外部テーブルへのアクセスは、通常の SQL 権限を使用して、通常のテーブルの場合と同様に許可または禁止することができます。

**例**: 次の例では、外部テーブルの作成方法を示します。

	CREATE EXTERNAL TABLE [dbo].[order_line]( 
		 [ol_o_id] int NOT NULL, 
		 [ol_d_id] tinyint NOT NULL,
		 [ol_w_id] int NOT NULL, 
		 [ol_number] tinyint NOT NULL, 
		 [ol_i_id] int NOT NULL, 
		 [ol_delivery_d] datetime NOT NULL, 
		 [ol_amount] smallmoney NOT NULL, 
		 [ol_supply_w_id] int NOT NULL, 
		 [ol_quantity] smallint NOT NULL, 
		 [ol_dist_info] char(24) NOT NULL 
	) 
	
	WITH 
	( 
		DATA_SOURCE = MyExtSrc, 
	 	SCHEMA_NAME = 'orders', 
	 	OBJECT_NAME = 'order_details', 
		DISTRIBUTION=SHARDED(ol_w_id)
	); 

次の例では、外部テーブルの一覧を現在のデータベースから取得する方法を示します。

	select * from sys.external_tables; 

## クエリ実行 

### 2\.1 完全に適合する T-SQL クエリ 

外部データ ソースと外部テーブルを定義すると、外部テーブルに対して完全に T-SQL を使用できるようになります。

**行方向のパーティション分割の例**: 次のクエリでは、倉庫、注文、および注文明細行の間で 3 方向結合を実行し、いくつかの集計と選択的フィルターを使用します。ここでは、(1) 行方向のパーティション分割 (シャーディング) のほか、(2) 倉庫、注文、および注文明細行がウェアハウスの ID 列でシャーディングされること、エラスティック クエリがシャード上の結合を併置できること、クエリの負荷の高い部分をシャード上で並列に処理できることを想定しています。

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
 
### 2\.2 ストアド プロシージャ SP\_EXECUTE\_FANOUT 

エラスティック クエリには、シャードへの直接アクセスを提供するストアド プロシージャも導入されています。sp\_execute\_fanout という名前のこのストアド プロシージャは、次のパラメーターを受け取ります。

* サーバー名 (nvarchar): シャード マップをホストする論理サーバーの完全修飾名。 
* シャード マップ データベース名 (nvarchar): シャード マップ データベースの名前。 
* ユーザー名 (nvarchar): シャード マップ データベースにログインするためのユーザー名。 
* パスワード (nvarchar): ユーザーのパスワード。 
* シャード マップ名 (nvarchar): クエリで使用するシャード マップの名前。 
*  クエリ: 各シャードで実行する T-SQL クエリ。 
*  パラメーター宣言 (nvarchar) (省略可能): (sp\_executesql などの) クエリ パラメーターで使用される、パラメーターのデータ型定義を含む文字列。 
*  パラメーター値のリスト (省略可能): (sp\_executesql などの) パラメーター値のコンマ区切りリスト。  

sp\_execute\_fanout は、呼び出しパラメーターに入力されたシャード マップ情報を使用して、指定された T-SQL ステートメントを、シャード マップに登録されているすべてのシャードで実行します。結果は、UNION ALL セマンティクスを使用してマージされます。結果には、シャード名の付いた "仮想" 列も取り込まれます。

シャード マップ データベースに接続する場合も、シャードに接続する場合も同じ資格情報が使用されます。

例:

	sp_execute_fanout 
		’myserver.database.windows.net', 
		N'ShardMapDb', 
		N'myuser', 
		N'MyPwd', 
		N'ShardMap', 
		N'select count(w_id) as foo from warehouse' 

## ツールの接続性  

通常の SQL Server 接続文字列を使用して、アプリケーション、BI、およびデータ統合ツールを、外部テーブル定義を持つデータベースに接続できます。ご使用のツールのデータ ソースとして SQL Server がサポートされていることを確認してください。次に、ツールに接続される他の SQL Server データベースと同様にエラスティック クエリ データベースを参照して、外部テーブルをローカル テーブルであるかのようにツールまたはアプリケーションから使用します。

## ベスト プラクティス 

* エラスティック クエリ エンドポイント データベースに、SQL データベース ファイアウォール経由でのシャード マップ データベースとすべてのシャードへのアクセスが許可されていることを確認します。  

* エラスティック クエリでは、外部テーブルで定義されたデータ分散を検証または適用しません。実際のデータ分散がテーブル定義に指定されたデータ分散と異なる場合、クエリが予期しない結果を生成する場合があります。

* シャーディング キーによる述語で特定のシャードを処理から安全に除外できる場合、エラスティック クエリでは、現在のところ、シャードの除去を実行しません。

* エラスティック クエリは、計算の大部分をシャード上で実行できるクエリに最適です。通常、最適なクエリ パフォーマンスが得られるのは、シャード上で評価可能な選択的なフィルター述語を使用した場合、またはすべてのシャード上でパーティション分割方法により実行可能な、パーティション分割キーによる結合を使用した場合となります。その他のクエリ パターンでは、シャードからヘッド ノードに大量のデータを読み込むことが必要になる場合があり、パフォーマンスが低下する可能性があります。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
<!--anchors-->

<!---HONumber=Oct15_HO4-->