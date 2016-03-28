<properties 
    pageTitle="エラスティック データベース プールの管理 (PowerShell) | Microsoft Azure" 
    description="PowerShell を使用してエラスティック データベース プールを管理する方法について説明します。"  
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="03/15/2016"
    ms.author="sstein"/>

# エラスティック データベース プールの監視と管理 (PowerShell) 

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)


PowerShell コマンドレットを使った[エラスティック データベース プール](sql-database-elastic-pool.md)の管理方法について説明します。

> [AZURE.NOTE] エラスティック データベース プールは現在プレビュー段階であり、SQL Database V12 サーバーでのみ使用できます。SQL Database V11 サーバーがある場合は、[PowerShell を使用して V12 へのアップグレードとプールの作成](sql-database-upgrade-server-portal.md)を 1 回の手順で実行できます。

Azure PowerShell 1.0 以降を実行している必要があります。詳細については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」をご覧ください。




## エラスティック データベース プールでの新しいエラスティック データベースの作成

プール内に直接新しいデータベースを作成するには、[New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) コマンドレットを使用して **ElasticPoolName** パラメーターを設定します。


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## エラスティック データベース プールへのスタンドアロン データベースの移動

プール内に既存のデータベースを移動するには、[Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) コマンドレットを使用して **ElasticPoolName** パラメーターを設定します。

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## エラスティック データベース プールのパフォーマンス設定の変更

エラスティック データベース プールのパフォーマンス設定を変更するには、[Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) コマンドレットを使用します。

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## エラスティック データベース プールの操作の状態の取得

[Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) コマンドレットを使用して、作成や更新など、エラスティック データベース プール操作の状態を追跡できます。

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## エラスティック データベースをエラスティック データベース プールに出し入れする際の状態の取得

[Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx) コマンドレットを使用して、作成や更新など、エラスティック データベース操作の状態を追跡できます。

	Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## エラスティック データベース プールの使用状況データを取得する

リソース プールの制限値のパーセンテージとして取得できるメトリックを以下に示します。

* 平均 CPU 使用率 - cpu\_percent 
* 平均 IO 使用率 - data\_io\_percent 
* 平均 Log 使用率 - log\_write\_percent 
* 平均メモリ使用率 - memory\_percent 
* 平均 eDTU 使用率 (CPU、IO、Log 使用率の最大値として) - DTU\_percent 
* 同時ユーザー リクエストの最大数 (ワーカー) - max\_concurrent\_requests 
* 同時ユーザー セッションの最大数 - max\_concurrent\_sessions 
* エラスティック プールの合計ストレージ容量 - storage\_in\_megabytes 


メトリックの単位と保有期間を以下に示します。

* 5 分間隔でデータが返されます。  
* データは 14 日間保持されます。  


このコマンドレットと API で 1 度の呼び出しで取得できる行数が 1000 行 (5 分間隔で 3 日分のデータ) に制限されます。ただし、このコマンドは何度も呼び出すことができ、より多くのデータを取得するためにさまざまな開始から終了までの間隔で指定できます。


次のメトリックを取得します。

	$metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

呼び出しを繰り返し、次のようなデータを追加することで日数を追加できます。

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
テーブルの書式を設定します。

    $table = Format-MetricsAsTable $metrics 

CSV ファイルにエクスポートします。

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

## エラスティック データベースのリソース消費メトリックの取得

これらの API は、次のセマンティックな相違点を除き、スタンドアロン データベースのリソース使用率の監視に使用する現在の (V12) API と同じです。

* 取得されたこの API メトリックは、エラスティック データベース プールの databaseDtuMax (または CPU、IO など基盤となるメトリックと同等の上限) セットのパーセンテージとして表されます。たとえば、これらのメトリックいずれかの使用率が 50% であることは、特定のリソース消費量が、DB の上限の 50% (親のエラスティック データベース プールで該当するリソースの制限) であることを示しています。 

メトリックを取得します。

    $metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

呼び出しを繰り返し、次のようなデータを追加することで、必要に応じて日数を追加できます。

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

テーブルの書式を設定します。

    $table = Format-MetricsAsTable $metrics 

CSV ファイルにエクスポートします。

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## エラスティック データベース プールの監視と管理 (PowerShell) の例


    $subscriptionId = '<Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'
    
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    
    Set-AzureRmSqlElasticPool –ResourceGroupName $resourceGroupName –ServerName $serverName –ElasticPoolName $poolName –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $databaseName 
    $startTime1 = '2/10/2016'
    $endTime1 = '2/14/2016'
    $startTime2 = '2/14/2016'
    $endTime2 = '2/18/2016'
    
    
    
    $metrics = (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics
    
    $metrics = $metrics + (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}
    
    $metrics = (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics = $metrics + (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## 次のステップ

- [エラスティック ジョブを作成する](sql-database-elastic-jobs-overview.md) エラスティック ジョブは、プール内にある任意の数のデータベースに対して T-SQL スクリプトの実行を容易にします。


## エラスティック データベースのリファレンス

API とエラーの詳細を含む弾力性データベースと弾力性データベース プールの詳細については、「[弾力性データベースのリファレンス](sql-database-elastic-pool-reference.md)」をご覧ください。

<!---HONumber=AcomDC_0316_2016-->