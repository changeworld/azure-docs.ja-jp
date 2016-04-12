<properties
   pageTitle="既存のデータベースをエラスティック データベース ツールを使用するように変換する"
   description="シャード マップ マネージャーを作成することで、エラスティック データベース ツールを使用するようにシャード化されたデータベースを変換します"
   services="sql-database"
   documentationCenter=""
   authors="SilviaDoomra"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="03/29/2016"
   ms.author="SilviaDoomra"/>

# 既存のデータベースをエラスティック データベース ツールを使用するように変換する

既存のスケールアウトされたシャーディング ソリューションがある場合、ここで説明する手法を使用して Elastic Database ツールを利用できます。変換が済むと、[Elastic Database クライアント ライブラリ](sql-database-elastic-database-client-library.md)と[分割/マージ ツール](sql-database-elastic-scale-overview-split-and-merge.md)を使用できるようになります。

これらの方法は、[.NET Framework クライアント ライブラリ](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)または「[Azure SQL DB - Elastic Database tools scripts (Azure SQL DB - Elastic Database ツール スクリプト)](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)」の PowerShell スクリプトを使用して実装できます。この例では、PowerShell スクリプトを使用します。

次の 4 つのステップがあります。

1. シャード マップ マネージャー データベースを準備します。
2. シャード マップを作成します。
3. 個々のシャードを準備します。  
2. シャード マップにマッピングを追加します。

ShardMapManager の詳細については、「[シャード マップの管理](sql-database-elastic-scale-shard-map-management.md)」を参照してください。エラスティック データベース ツールの概要については、「[Elastic Database 機能の概要](sql-database-elastic-scale-introduction.md)」を参照してください。

## シャード マップ マネージャー データベースの準備
新しいデータベースまたは既存のデータベースをシャード マップ マネージャーとして使用できます。これは 1 回限りの操作です。

## 手順 1: シャード マップ マネージャーを作成する
シャード マップ マネージャーとして機能するデータベースは、シャードと同じデータベースにしてはならないことに注意してください。

	# Create a shard map manager. 
	New-ShardMapManager -UserName '<user_name>' 
	-Password '<password>' 
	-SqlServerName '<server_name>' 
	-SqlDatabaseName '<smm_db_name>' 
	#<server_name> and <smm_db_name> are the server name and database name 
	# for the new or existing database that should be used for storing 
	# tenant-database mapping information.

### シャード マップ マネージャーを取得するには

作成した後は、次のコマンドレットでシャード マップ マネージャーを取得できます。この手順は、ShardMapManager オブジェクトを使用する必要があるたびに必要です。

	# Try to get a reference to the Shard Map Manager  
	$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
	-Password '<password>' 
	-SqlServerName '<server_name>' 
	-SqlDatabaseName '<smm_db_name>' 

  
## 手順 2: シャード マップを作成する

次のいずれかのモデルを作成します。

1. データベースごとに 1 つのテナント 
2. データベースごとに複数のテナント (2 種類):
	3. 範囲マッピング
	4. リスト マッピング
 

シングルテナント データベース モデルを使用している場合は、リスト マッピングを使用します。シングルテナント モデルでは、テナントごとに 1 つのデータベースが割り当てられます。これは、管理が簡単なので、SaaS 開発者に有効なモデルです。

![リスト マッピング][1]

これに対し、マルチテナント データベース モデルでは、1 つのデータベースに複数のテナントが割り当てられます (そして、テナントのグループを複数のデータベースに分散させることができます)。これは、テナントごとのデータの量が少ないと予測される場合に実行可能なモデルです。このモデルでは、*範囲マッピング*を使用してデータベースにテナントの範囲を割り当てます。
 

![範囲マッピング][2]

リスト マッピングを使用して複数のテナントを 1 つのデータベースに割り当てて、マルチテナント データベース モデルを実装することもできます。たとえば、ID が 1 と 5 のテナントに関する情報を DB1 に格納し、DB2 にテナント 7 と 10 のデータを格納する、といったことができます。

![単一 DB 上の複数のテナント][3]


## 手順 2、オプション 1: リスト マッピングのシャード マップを作成する
ShardMapManager オブジェクトを使用してシャード マップを作成します。

	# $ShardMapManager is the shard map manager object. 
	$ShardMap = New-ListShardMap -KeyType $([int]) 
	-ListShardMapName 'ListShardMap' 
	-ShardMapManager $ShardMapManager 
 
 
## 手順 2、オプション 2: 範囲マッピングのシャード マップを作成する

このマッピング パターンを使用する場合は、テナント ID の値が連続した範囲である必要があることに注意してください。データベースを作成するときに範囲をスキップすることで、範囲にギャップが存在していてもかまいません。

	# $ShardMapManager is the shard map manager object 
	# 'RangeShardMap' is the unique identifier for the range shard map.  
	$ShardMap = New-RangeShardMap 
	-KeyType $([int]) 
	-RangeShardMapName 'RangeShardMap' 
	-ShardMapManager $ShardMapManager 

## 手順 2、オプション 3: 単一データベースでのリスト マッピング
このパターンを設定するには、手順 2、オプション 1 で示したようなリスト マップも作成する必要があります。

## 手順 3: 個々のシャードを準備する

各シャード (データベース) をシャード マップ マネージャーに追加します。これは、マッピング情報を格納するための個々のデータベースの準備です。各シャードで次のメソッドを実行します。
	 
	Add-Shard 
	-ShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>'
	# The $ShardMap is the shard map created in step 2.
 

## 手順 4: マッピングを追加する

マッピングの追加は、作成したシャード マップの種類によって異なります。リスト マップを作成した場合は、リスト マッピングを追加します。範囲マップを作成した場合は、範囲マッピングを追加します。

### 手順 4、オプション 1: リスト マッピングのデータをマップする

各テナントのリスト マッピングを追加することで、データをマップします。

	# Create the mappings and associate it with the new shards 
	Add-ListMapping 
	-KeyType $([int]) 
	-ListPoint '<tenant_id>' 
	-ListShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>' 

### 手順 4、オプション 2: 範囲マッピングのデータをマップする

すべてのテナント ID 範囲の範囲マッピングを追加します (データベースの関連付け)。

	# Create the mappings and associate it with the new shards 
	Add-RangeMapping 
	-KeyType $([int]) 
	-RangeHigh '5' 
	-RangeLow '1' 
	-RangeShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>' 


### 手順 4、オプション 3: 単一データベースの複数テナントのデータをマップする

各テナントに対し、Add-ListMapping を実行します (上記オプション 1)。


## マッピングの確認

既存のシャードおよびそれらに関連付けられているマッピングに関する情報は、次のコマンドを使用してクエリできます。

	# List the shards and mappings 
	Get-Shards -ShardMap $ShardMap 
	Get-Mappings -ShardMap $ShardMap 

## まとめ

セットアップが完了すると、Elastic Database クライアント ライブラリの使用を開始できます。[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)および[マルチシャード クエリ](sql-database-elastic-scale-multishard-querying.md)を使用することもできます。

## 次のステップ


「[Azure SQL DB - Elastic Database tools scripts (Azure SQL DB - Elastic Database ツール スクリプト)](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)」から PowerShell スクリプトを取得します。

ツールは GitHub ([Azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools)) にもあります。

マルチテナント モデルとシングルテナント モデルの間でデータを移動するには、分割/マージ ツールを使用します。[分割/マージ ツール](sql-database-elastic-scale-get-started.md)を参照してください。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
 

<!---HONumber=AcomDC_0330_2016------>