<properties 
    pageTitle="T-SQL を使用したエラスティック プール内への Azure SQL データベースの作成または移動 | Microsoft Azure" 
    description="T-SQL を使用してエラスティック プール内に Azure SQL データベースを作成します。または、T-SQL を使用してエラスティック プールにデータベースを移動したり、エラスティック プールからデータベースを移動したりします。" 
	services="sql-database" 
    documentationCenter="" 
    authors="sidneyh" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="04/01/2016"
    ms.author="sidneyh"/>

# Transact-SQL を使用したエラスティック データベース プールの監視と管理  

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-elastic-pool-manage-portal.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

データベースをエラスティック プールに作成したりエラスティック プールから移動したりするには、[Create Database (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) コマンドと [Alter Database (Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) コマンドを使います。これらのコマンドを使用する前に、エラスティック プールが存在している必要があります。これらのコマンドは、データベースのみに作用します。T-SQL コマンドを使用して新しいプールを作成したりプールのプロパティ (最小 eDTU、最大 eDTU など) の設定を変更したりすることはできません。


> [AZURE.NOTE] エラスティック データベース プールは現在プレビュー段階であり、SQL Database V12 サーバーでのみ使用できます。SQL Database V11 サーバーがある場合は、[PowerShell を使用して V12 へのアップグレードとプールの作成](sql-database-upgrade-server-portal.md)を 1 回の手順で実行できます。


## エラスティック プールでの新しいデータベースの作成
CREATE DATABASE コマンドと共に SERVICE\_OBJECTIVE オプションを使用します。

	CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
	-- Create a database named db1 in a pool named S3M100.

エラスティック プール内のすべてのデータベースが、エラスティック プールのサービス レベル (Basic、Standard、Premium) を継承します。


## エラスティック プール間でのデータベースの移動
ALTER DATABASE コマンドと共に MODIFY を使用します。SERVICE\_OBJECTIVE オプションに ELASTIC\_POOL を設定し、name にターゲット プールの名前を設定します。

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
	-- Move the database named db1 to a pool named P1M125  


## エラスティック プールへのデータベースの移動 
ALTER DATABASE コマンドと共に MODIFY を使用します。SERVICE\_OBJECTIVE オプションに ELASTIC\_POOL を設定し、name にターゲット プールの名前を設定します。

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
	-- Move the database named db1 to a pool named S3100.

## エラスティック プールからのデータベースの移動
ALTER DATABASE コマンドを使用し、SERVICE\_OBJECTIVE をいずれかのパフォーマンス レベル (S0、S1 など) に設定します。

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
	-- Changes the database into a stand-alone database with the service objective S1.

## エラスティック プール内のデータベースの一覧表示
エラスティック プール内のデータベースを一覧表示するには、[sys.database\_service \_objectives ビュー](https://msdn.microsoft.com/library/mt712619)を使用します。ビューを照会するためにマスター データベースにログインします。

>[AZURE.NOTE] 現在、エラスティック プール内のデータベースの service\_objective\_column は、サービス目標文字列の内部トークンを返します。このトークンは、文字列 "ElasticPool" で置き換えられます。
>

	SELECT d.name, slo.*  
	FROM sys.databases d 
	JOIN sys.database_service_objectives slo  
	ON d.database_id = slo.database_id
	WHERE elastic_pool_name = 'MyElasticPool'; 

## エラスティック プールのリソース使用量の監視

論理サーバーのエラスティック プールのリソース使用量の統計を確認するには、[sys.elastic\_pool \_resource \_stats ビュー](https://msdn.microsoft.com/library/mt280062.aspx)を使用します。ビューを照会するためにマスター データベースにログインします。

	SELECT * FROM sys.elastic_pool_resource_stats 
	WHERE elastic_pool_name = 'MyElasticPool'
	ORDER BY end_time DESC;

## エラスティック プール内のデータベースのリソース使用量の監視
エラスティック プール内のデータベースのリソース使用量の統計を確認するには、[sys.dm \_resource\_stats ビュー](https://msdn.microsoft.com/library/dn800981.aspx)または [sys.resource \_stats ビュー](https://msdn.microsoft.com/library/dn269979.aspx)を使用します。このプロセスは、任意の 1 つのデータベースのリソース使用量を照会する操作に似ています。

## 次のステップ

エラスティック データベース プールを作成した後は、エラスティック ジョブを作成してプール内のエラスティック データベース を管理できます。エラスティック ジョブはプールのデータベースの任意の数に対して T-SQL スクリプトの実行を容易にします。詳細については、「[エラスティック データベース ジョブの概要](sql-database-elastic-jobs-overview.md)」をご覧ください。

<!---HONumber=AcomDC_0406_2016-->