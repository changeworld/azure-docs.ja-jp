<properties
	pageTitle="シャーディング (水平的パーティション分割) のためのエラスティック クエリの概要 | Microsoft Azure"
	description="データベース間データベース クエリを使用する方法"
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
	ms.author="SilviaDoomra" />

# シャーディング (水平的パーティション分割) のためのエラスティック クエリの概要

Azure SQL Database 用の エラスティック データベース クエリ (プレビュー) を使用すると、1 つの接続ポイントで複数のデータベースにまたがる T-SQL クエリを実行することができます。エラスティック データベース クエリ機能の詳細については、「[機能の概要に関するページ](sql-database-elastic-query-overview.md)」を参照してください。

このトピックは、「[エラスティック データベース ツールの概要](sql-database-elastic-scale-get-started.md)」に示したサンプルを拡張したものです。終了すると、多くの関連するデータベースにまたがるクエリを実行するために Azure SQL Database を構成および使用する方法を習得できます。
## 前提条件

[「エラスティック データベース ツールの概要」に示されているサンプル](sql-database-elastic-scale-get-started.md)をダウンロードして実行します。

## サンプル アプリケーションを使用してシャード マップ マネージャーを作成する

ここでは、シャード マップ マネージャーといくつかのシャードを作成し、シャードにデータを挿入します。シャードがすでにセットアップされ、シャード データが取り込まれている場合は、以下の手順を省略して、次のセクションに進むことができます。

1. 「**「エラスティック データベース ツールの概要**」に示されているサンプル アプリケーションをビルドして実行します。セクション「[サンプル アプリケーションのダウンロードと実行](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools)」の手順 7 まで実行します。手順 7 を終了すると、次のコマンド プロンプトが表示されます。

	![コマンド プロンプト][1]

2.  コマンド ウィンドウで、「1」を入力し、**Enter** キーを押します。シャード マップ マネージャーが作成され、2 つのシャードがサーバーに追加されます。「3」を入力し、**Enter** キーを押します。この操作を 4 回を繰り返します。これにより、サンプルのデータ行がシャードに挿入されます。
3.  [Azure ポータル](https://portal.azure.com) に、v12 サーバーにある次の 3 つの新しいデータベースが表示されるはずです。

	![Visual Studio の確認][2]

	この時点で、複数のデータベースにまたがるクエリは、エラスティック データベース クライアント ライブラリを介してサポートされます。たとえば、コマンド ウィンドウのオプション 4 を使用するとします。マルチ シャード クエリの結果は常に、すべてのシャードからの結果に **UNION ALL** を適用したものとなります。

	次のセクションでは、複数のシャードにまたがって実行されるデータの高度なクエリをサポートするサンプル データベース エンドポイントを作成します。

## エラスティック クエリ データベースを作成する

1. [Azure ポータル](https://portal.azure.com)を開き、ログインします。
2. シャード セットアップと同じサーバーに新しい Azure SQL Database を作成します。このデータベースに "ElasticDBQuery" という名前を付けます。 

	![Azure ポータルと価格レベル][3]

	注: 既存のデータベースを使用できます。そうする場合、その既存のプレミアム データベースを、クエリの実行対象とするシャードに含めることはできません。このデータベースは、エラスティック データベース クエリのメタデータ オブジェクトを作成するために使用されます。


## データベース オブジェクトを作成する

### データベース スコープのマスター キーと資格情報

これらを使用し、次の手順に従ってシャード マップ マネージャーとシャードに接続します。

1. SQL Server Management Studio または Visual Studio の SQL Server Data Tools を開きます。
2. ElasticDBQuery データベースに接続し、次の T-SQL コマンドを実行します。

		CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

		CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
		WITH IDENTITY = '<username>',
		SECRET = '<password>';

	"username" と "password" は、「[エラスティック データベース ツールの概要](sql-database-elastic-scale-get-started.md)」の「[サンプル アプリケーションのダウンロードと実行](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools)」の手順 6 で使用するログイン情報と同じである必要があります。

### 外部データ ソース

外部データ ソースを作成するには、ElasticDBQuery データベースで、次のコマンドを実行します。

	CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
	  CREDENTIAL = ElasticDBQueryCred,
 	  SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 エラスティック データベース ツールのサンプルを使用してシャード マップとシャード マップ マネージャーを作成した場合、"CustomerIDShardMap" はシャード マップの名前となります。ただし、このサンプルでカスタム セットアップを使用した場合は、アプリケーションで選択するシャード マップ名となります。

### 外部テーブル

シャード上の Customers テーブルと一致する外部テーブルを作成するには、ElasticDBQuery データベースに対して次のコマンドを実行します。

	CREATE EXTERNAL TABLE [dbo].[Customers]
	( [CustomerId] [int] NOT NULL,
	  [Name] [nvarchar](256) NOT NULL,
	  [RegionId] [int] NOT NULL)
	WITH
	( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
	) ;

## サンプルのエラスティック データベース T-SQL クエリを実行する

外部データ ソースと外部テーブルを定義すると、外部テーブルに対して完全に T-SQL を使用できるようになります。

ElasticDBQuery データベースでこのクエリを実行します。

	select count(CustomerId) from [dbo].[Customers]

クエリはすべてのシャードからの結果を集計し、次のように出力を表示します。

![出力の詳細][4]

## エラスティック データベース クエリの結果を Excel にインポートする

 クエリの結果は Excel ファイルにインポートすることができます。

1. Excel 2013 を起動します。
2. 	**[データ]** リボンに移動します。
3. 	**[その他のデータ ソース]** をクリックし、**[SQL Server]** をクリックします。

	![他のソースから Excel へのインポート][5]
4. 	**[データ接続ウィザード]** で、サーバー名とログイン時の資格情報を入力します。その後、**[次へ]** をクリックします。
5. 	**[使用するデータが含まれているデータベースを選択]** ダイアログ ボックスで、**[ElasticDBQuery]** データベースを選択します。
6. 	リスト ビューで **[Customers]** テーブルを選択し、**[次へ]** をクリックします。**[完了]** をクリックします。
7. 	**[データのインポート]** フォームの **[このデータをブックでどのように表示するかを選択してください。]** で、**[テーブル]** を選択し、**[OK]** をクリックします。

さまざまなシャードに格納されている、**[Customers]** テーブルからのすべての行が Excel シートに読み込まれます。

## 次のステップ
これで Excel の強力なデータ視覚化機能を使用できるようになりました。サーバー名、データベース名、および資格情報が含まれる接続文字列を使用して、BI とデータ統合ツールをエラスティック クエリ データベースに接続することができます。ご使用のツールのデータ ソースとして SQL Server がサポートされていることを確認してください。エラスティック クエリ データベースおよび外部テーブル (ツールを使用して接続するその他の SQL Server データベースおよび SQL Server テーブルのような) を参照できます。

### コスト
Elastic Database クエリ機能を使用する場合に追加の料金は発生しません。

料金情報については、「[SQL Database の料金詳細](http://azure.microsoft.com/pricing/details/sql-database/)」を参照してください。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->

<!---HONumber=AcomDC_0107_2016-->