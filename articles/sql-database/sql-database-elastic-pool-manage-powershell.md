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
    ms.date="05/10/2016"
    ms.author="sidneyh"/>

# PowerShell でのエラスティック データベース プールの監視と管理 

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

PowerShell コマンドレットを使用して、[エラスティック データベース プール](sql-database-elastic-pool.md)を管理します。

一般的なエラー コードについては、「[SQL Database クライアント アプリケーションの SQL エラー コード: データベース接続エラーとその他の問題](sql-database-develop-error-messages.md)」をご覧ください。

プールに関する値については、「[eDTU and storage limits (eDTU とストレージの制限)](sql-database-elastic-pool#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)」を参照してください。

## 前提条件

* Azure PowerShell 1.0 以降。詳細については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」をご覧ください。
* エラスティック データベース プールは、SQL Database V12 サーバーでのみ使用できます。SQL Database V11 サーバーがある場合は、[PowerShell を使用して V12 へのアップグレードとプールの作成](sql-database-upgrade-server-portal.md)を 1 回の手順で実行できます。


## エラスティック プールへのデータベースの移動

プールへの、またはプールからのデータベースの移動は、[Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) で行うことができます。

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## プールのパフォーマンス設定を変更する

パフォーマンスが低下したときは、成長に合わせてプールの設定を変更できます。[Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) コマンドレットを使用します。-Dtu パラメーターにプールあたりの eDTU 数を設定します。使用可能な値については、「[eDTU and storage limits (eDTU とストレージの制限)](sql-database-elastic-pool#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)」を参照してください。

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## プール操作の状態を取得する

プールの作成には時間がかかることがあります。作成や更新など、プール操作の状態を追跡するには、[Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) コマンドレットを使用します。

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## エラスティック データベースをプールに出し入れする際の状態を取得する

データベースの移動には時間がかかることがあります。移動の状態を追跡するには、[Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx) コマンドレットを使用します。

	Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## プールのリソース使用状況データを取得する

リソース プールの制限値のパーセンテージとして取得できるメトリックを以下に示します。


| メトリックの名前 | 説明 |
| :-- | :-- |
| cpu\_percent | プールの限度に対する割合で表した平均コンピューティング使用率。 |
| physical\_data\_read\_percent | プールの限度に対する割合で表した平均 I/O 使用率。 |
| log\_write\_percent | プールの限度に対する割合で表した平均書き込みリソース使用率。 | 
| DTU\_consumption\_percent | プールの eDTU 限度に対する割合で表した平均 eDTU 使用率。 | 
| storage\_percent | プールのストレージ限度に対する割合で表した平均ストレージ使用率。 |  
| workers\_percent | プールの限度に対する割合で表した最大同時実行ワーカー (要求) 数。 |  
| sessions\_percent | プールの限度に対する割合で表した最大同時実行セッション数。 | 
| eDTU\_limit | この期間中のこのエラスティック プールに対する現在の最大エラスティック プール DTU 設定。 |
| storage\_limit | この期間中のこのエラスティック プールに対する現在の最大エラスティック プール ストレージ制限 (メガバイト単位)。 |
| eDTU\_used | この期間にプールによって使用された平均 eDTU 数。 |
| storage\_used | この期間にプールによって使用された平均ストレージ (バイト単位)。 |

**メトリックの単位と保有期間を以下に示します。**

* 5 分間隔でデータが返されます。  
* データは 14 日間保持されます。  

このコマンドレットと API で 1 度の呼び出しで取得できる行数が 1000 行 (5 分間隔で 3 日分のデータ) に制限されます。ただし、このコマンドは何度も呼び出すことができ、より多くのデータを取得するためにさまざまな開始から終了までの間隔で指定できます。

メトリックを取得するには:

	$metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  


## エラスティック データベースのリソース使用状況データを取得する

これらの API は、次のセマンティックな相違点を除き、スタンドアロン データベースのリソース使用率の監視に使用する現在の (V12) API と同じです。

この API では、取得されたメトリックが、プールに設定されている最大 eDTU 数 (または CPU、IO など、基盤となるメトリックに関してこれと同等の役割を果たす上限) に対するパーセンテージで表示されます。たとえば、これらのメトリックのうちのいずれかの使用率が 50% であることは、特定のリソースの消費量が、親となるプールでそのリソースに対して設けられているデータベースの上限の 50% であることを示しています。

メトリックを取得するには:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

## サブスクリプション内の複数のプールのリソース使用状況データを収集して監視する

サブスクリプションに多数のデータベースがある場合、各エラスティック プールを個別に監視するのは面倒です。代わりに、SQL Database の PowerShell コマンドレットと T-SQL クエリを組み合わせて、複数のプールおよびそのデータベースからリソース使用状況データを収集し、リソースの使用状況を監視および分析できます。そのような PowerShell スクリプトの[実装のサンプル](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)が、内容と方法についてのドキュメントと共に GitHub の SQL Server サンプル リポジトリにあります。

このサンプル実装を使用するには次のようにします。


1. [スクリプトとドキュメント](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)をダウンロードします。
2. 環境に合わせてスクリプトを変更します。エラスティック プールがホストされている 1 つまたは複数のサーバーを指定します。
3. 収集されたメトリックを格納するテレメトリ データベースを指定します。 
4. スクリプトをカスタマイズして、スクリプトの実行期間を指定します。

スクリプトが行うことの概要は次のとおりです。

*	指定された Azure サブスクリプション (または指定されたサーバーの一覧) のすべてのサーバーを列挙します。
*	各サーバーに対してバック グラウンド ジョブを実行します。ジョブは一定の間隔で繰り返し実行し、サーバー内のすべてのプールのテレメトリ データを収集します。その後、収集されたデータを指定されたテレメトリ データベースに読み込みます。
*	各プール内のデータベースの一覧を列挙し、データベース リソース使用状況データを収集します。その後、収集されたデータをテレメトリ データベースに読み込みます。

テレメトリ データベースの収集されたメトリックを分析して、エラスティック プールとその中のデータベースの状態を監視できます。スクリプトでは、指定された時間範囲のメトリックの集計を支援する定義済みのテーブル値関数 (TVF) もテレメトリ データベースにインストールされます。たとえば、TVF の結果を使用すると、"指定した時間範囲で eDTU 使用率が高い上位 N 個のエラスティック プール" を表示できます。 必要に応じて、Excel や Power BI などの分析ツールを使用して、収集されたデータをクエリおよび分析します。

## 例: プールとそのデータベースのリソース消費メトリックを取得する

この例では、特定のエラスティック プールとそのすべてのデータベースの消費メトリックを取得します。収集されたデータは書式設定されて .csv 形式のファイルに書き込まれます。ファイルは Excel で表示できます。

	$subscriptionId = '<Azure subscription id>'	      # Azure subscription ID
	$resourceGroupName = '<resource group name>'             # Resource Group
	$serverName = <server name>                              # server name
	$poolName = <elastic pool name>                          # pool name
		
	# Login to Azure account and select the subscription.
	Login-AzureRmAccount
	Set-AzureRmContext -SubscriptionId $subscriptionId
	
	# Get resource usage metrics for an elastic pool for the specified time interval.
	$startTime = '4/27/2016 00:00:00'  # start time in UTC
	$endTime = '4/27/2016 01:00:00'    # end time in UTC
	
	# Construct the pool resource ID and retrive pool metrics at 5 minute granularity.
	$poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
	$poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime) 
	
	# Get the list of databases in this pool.
	$dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName
	
	# Get resource usage metrics for a database in an elastic database for the specified time interval.
	$dbMetrics = @()
	foreach ($db in $dbList)
	{
	    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
	    $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
	}
	
	#Optionally you can format the metrics and output as .csv file using the following script block.
	$command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) { 
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name; 
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId 
          }
          $table = $table += $sx
      }
    }
    
    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
	}
	
	# Format and output pool metrics
	Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv
	
	# Format and output database metrics
	Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv



## エラスティック プール操作の待機時間

- 通常、データベースあたりの最小 eDTU またはデータベースあたりの最大 eDTU の変更は、5 分以内で完了します。
- プールあたりの eDTU の変更は、プール内のすべてのデータベースで使用される領域の合計に依存します。変更の平均時間は、100 GB あたり 90 分以下です。たとえば、プール内のすべてのデータベースで使用される領域の合計が 200 GB の場合、プールあたりの eDTU の変更にかかる想定待機時間は、3 時間以下になります。

## V11 から V12 サーバーに移行する

PowerShell コマンドレットを使用して、V11 を含め、V12 より前のバージョンから V12 への Azure SQL Database のアップグレードを開始、停止、監視できます。

- [PowerShell を使用し、SQL Database V12 にアップグレードします](sql-database-upgrade-server-powershell.md)

これらの PowerShell コマンドレットについては、次のリファレンス ドキュメントを参照してください。


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


Stop- コマンドレットは、一時停止ではなく取り消しを意味します。アップグレードを再開する方法は、最初からやり直す方法以外にありません。Stop- コマンドレットにより、該当するすべてのリソースがクリーンアップされ、解放されます。

## 次のステップ

- [エラスティック ジョブを作成する](sql-database-elastic-jobs-overview.md): エラスティック ジョブを使用すると、プール内にある任意の数のデータベースに対して T-SQL スクリプトを実行できます。
- エラスティック データベース ツールを使用してスケールアウト、データの移動、クエリ、トランザクションの作成を行う方法については、「[Azure SQL Database によるスケール アウト](sql-database-elastic-scale-introduction.md)」を参照してください。

<!---HONumber=AcomDC_0511_2016-->