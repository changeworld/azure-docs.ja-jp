<properties
	pageTitle="プールに適した Single Database を特定する PowerShell スクリプト"
	description="弾力性データベース プールは、弾力性データベースのグループで共有される使用可能なリソースのコレクションです。このドキュメントでは、データベースのグループに対して、エラスティック データベース プールを使用することが適切であるか評価する PowerShell スクリプトについて説明します。"
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/16/2016"
	ms.author="jeffreyg"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>

# エラスティック データベース プールに適したデータベースを識別する PowerShell スクリプト

この記事では、SQL Database サーバーのユーザー データベースの合計 eDTU 値を評価するため PowerShell スクリプトのサンプルを示します。スクリプトは実行中にデータを収集します。一般的な運用環境のワークロードの場合、少なくとも 1 日間スクリプトを実行することをお勧めします。理想的には、データベースの一般的なワークロードを表す期間 (つまり、データベースの通常使用状況とピーク時の使用状況を表すデータをキャプチャできる期間)、スクリプトを実行します。1 週間またはさらに長期間スクリプトを実行すると、より正確な推計が得られる可能性が高くなります。

このスクリプトは、プールがサポートされていない v11 サーバー上のデータベースを評価する場合、またはプールがサポートされていない v12 サーバーに移行する場合に特に便利です。v12 サーバーの場合、SQL Database には、履歴の使用状況テレメトリを分析し、よりコスト効率が高くなるプールを推奨する組み込みインテリジェンスがあります。この機能の使用方法については、「[エラスティック データベース プールの監視、管理およびサイズ設定](sql-database-elastic-pool-manage-portal.md)」を参照してください。

> [AZURE.IMPORTANT] スクリプトの実行中は、PowerShell ウィンドウを開いたままにする必要があります。スクリプトの実行期間が目的の時間に達するまで、PowerShell ウィンドウは閉じないでください。

## 前提条件 

スクリプトの実行前に、次をインストールしてください。

- 最新の [Powershell コマンド ライン ツール](http://go.microsoft.com/?linkid=9811175&clcid=0x409)。
- [SQL Server 2014 Feature Pack](https://www.microsoft.com/download/details.aspx?id=42295)。

### スクリプトの詳細

スクリプトは、ローカル コンピューターまたはクラウド上の VM から実行できます。ローカル コンピューターから実行する場合、スクリプトは、ターゲット データベースからデータをダウンロードする必要があるためデータ送信費用が発生する可能性があります。次に、ターゲット データベースの数と、スクリプトの実行期間に基づくデータ量の評価を示します。Azure のデータ転送コストの詳細については、「[Data Transfers (データ転送) の料金詳細](https://azure.microsoft.com/pricing/details/data-transfers/)」を参照してください。
       
 -     1 データベース 1 時間あたり = 38 KB
 -     1 データベース 1 日あたり = 900 KB
 -     1 データベース 1 週間あたり = 6 MB
 -     100 データベース 1 日あたり = 90 MB
 -     500 データベース 1 週間あたり = 3 GB

このスクリプトで、Standard レベルの現在のパブリック プレビュー プランに適していないデータベースが除外されます。対象サーバーから他のデータベースも除外する場合は、条件に合わせてスクリプトを変更してください。既定では、スクリプトは次の情報はコンパイルしません。

* Elastic Database (エラスティック プール内に既にあるデータベース)
* サーバーの master データベース

スクリプトには、分析用の中間データを格納する出力用のデータベースが必要です。新規または既存のデータベースを使用できます。ツールの実行には技術的に不要ですが、分析結果への影響を回避するために、出力データベースは別のサーバーに配置する必要があります。出力データベースのパフォーマンス レベルは最低 S0 以上にする必要があります。大量のデータベースのデータを長時間収集する場合、出力データベースのパフォーマンス レベルを高いものにアップグレードすることを検討するとよいでしょう。

スクリプトには、ターゲット サーバー (エラスティック データベース プールの候補) に接続するための <*データベース名*>**.database.windows.net** などのような完全なサーバー名の資格情報を指定する必要があります。スクリプトでは一度に複数のサーバーを分析できません。

初期パラメーター値のセットの送信後には、Azure アカウントにログオンするように求められます。これは出力データベース サーバーではなくターゲット サーバーにログオンするためのものです。
	
スクリプトの実行時、次の警告が示された場合は無視してかまいません。

- 警告: Switch-AzureMode コマンドレットは廃止予定です。
- 警告: SQL Server サービスの情報を取得できませんでした。'Microsoft.Azure.Commands.Sql.dll' 上の WMI に接続しようとすると、「RPC サーバーを利用できません。」というエラーで失敗します。

スクリプトが完了すると、ターゲット サーバー内のすべての候補データベースを格納するために、プールに必要な推定 eDTU 数が出力されます。この推定 eDTU をプールの作成と構成に使用できます。プールが作成され、プールにデータベースを移動した後には、数日間厳密に監視する必要があります。また、必要に応じてプール eDTU 構成を調整します。「[エラスティック データベース プールの監視、管理およびサイズ設定](sql-database-elastic-pool-manage-portal.md)」を参照してください。


   [AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)
    
    param (
    [Parameter(Mandatory=$true)][string]$AzureSubscriptionName, # Azure Subscription name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$ResourceGroupName, # Resource Group name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$servername, # full server name like "abcdefg.database.windows.net"
    [Parameter(Mandatory=$true)][string]$username, # user name
    [Parameter(Mandatory=$true)][string]$serverPassword, # password
    [Parameter(Mandatory=$true)][string]$outputServerName, # metrics collection database for analysis. full server name like "zyxwvu.database.windows.net"
    [Parameter(Mandatory=$true)][string]$outputdatabaseName,
    [Parameter(Mandatory=$true)][string]$outputDBUsername,
    [Parameter(Mandatory=$true)][string]$outputDBpassword,
    [Parameter(Mandatory=$true)][int]$duration_minutes # How long to run. Recommend to run for the period of time when your typical workload is running. At least 10 mins.
    )
    
    Add-AzureAccount 
    Select-AzureSubscription $AzureSubscriptionName
    Switch-AzureMode AzureResourceManager
    
    $server = Get-AzureSqlServer -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    
    # Check version/upgrade status of the server
    $upgradestatus = Get-AzureSqlServerUpgrade -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    $version = ""
    if ([string]::IsNullOrWhiteSpace($server.ServerVersion)) 
    {
    $version = $upgradestatus.Status
    }
    else
    {
    $version = $server.ServerVersion
    }
    
    # For Elastic database pool candidates, we exclude master, and any databases that are already in a pool. You may add more databases to the excluded list below as needed
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool")}
    
    $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
    $destinationTableName = "resource_stats_output"
    
    # Create a table in output database for metrics collection
    $sql = "
    IF  NOT EXISTS (SELECT * FROM sys.objects 
    WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
    
    BEGIN
    Create Table $($destinationTableName) (database_name varchar(128), slo varchar(20), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
    Create Clustered Index ci_endtime ON $($destinationTableName) (end_time);
    END
    TRUNCATE TABLE $($destinationTableName);
    "
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 120 
    
    # waittime (minutes) is interval between data collection queries in the loop below.
    $Waittime = 10
    $end_Time = [DateTime]::UtcNow
    $start_time = $end_time.AddMinutes(-$Waittime)
    $finish_time = $end_Time.AddMinutes($duration_minutes)
    
    While ($end_time -lt $finish_time)
    {
    Write-Host "Collecting metrics..." 
    foreach ($db in $ListOfDBs)
    {
    if ($version -in ("12.0", "Completed")) # for V12 databases 
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'S3' THEN 100
    WHEN 'P1' THEN 125
    WHEN 'P2' THEN 250
    WHEN 'P3' THEN 1000
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
    SELECT @dbname as database_name, @SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    else
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'P1' THEN 100
    WHEN 'P2' THEN 200
    WHEN 'P3' THEN 800
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
    SELECT @dbname as database_name, @SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    
    $result = Invoke-Sqlcmd -ServerInstance $servername -Database $db.DatabaseName -Username $username -Password $serverPassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 3600 
    #bulk copy the metrics to output database
    $bulkCopy = new-object ("Data.SqlClient.SqlBulkCopy") $outputConnectionString 
    $bulkCopy.BulkCopyTimeout = 600
    $bulkCopy.DestinationTableName = "$destinationTableName";
    $bulkCopy.WriteToServer($result);
    
    }
    
    $start_time = $start_time.AddMinutes($Waittime)
    $end_time = $end_time.AddMinutes($Waittime)
    Write-Host $start_time
    Write-Host $end_time
    do {
    Start-Sleep 1
       }
    until (([DateTime]::UtcNow) -ge $end_time)
    }
    
    Write-Host "Analyzing the collected metrics...."
    # Analysis query that does aggregation of the resource metrics to calculate pool size.
    $sql1 = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
    WITH group_stats AS
    (
    SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
    FROM resource_stats_output 
    WHERE slo LIKE '
    
    $sql2 = '
    GROUP BY end_time
    )
    -- calculate aggregate storage and DTUs for all DBs in the group
    , group_DTU AS
    (
    SELECT end_time, avg_group_Storage, 
    (SELECT Max(v)
       FROM (VALUES (avg_group_cpu), (avg_group_log), (avg_group_io)) AS value(v)) AS avg_group_DTU
    FROM group_stats
    )
    -- Get top 1 percent of the storage and DTU utilization samples.
    , top1_percent AS (
    SELECT TOP 1 PERCENT avg_group_Storage, avg_group_dtu FROM group_dtu ORDER BY [avg_group_DTU] DESC
    )
    
    -- Max and 99th percentile DTU for the given list of databases if converted into an elastic pool. Storage is increased by factor of 1.25 to accommodate for future growth. Currently storage limit of the pool is determined by the amount of DTUs based on 1GB/DTU.
    --SELECT MAX(avg_group_Storage)*1.25/1024.0 AS Group_Storage_DTU, MAX(avg_group_dtu) AS Group_Performance_DTU, MIN(avg_group_dtu) AS Group_Performance_DTU_99th_percentile FROM top1_percent;
    SELECT @DTU_Storage = MAX(avg_group_Storage)*1.25/1024.0, @DTU_Perf_99 = MIN(avg_group_dtu) FROM top1_percent;
    IF @DTU_Storage > @DTU_Perf_99 
    SELECT ''Total number of DTUs dominated by storage: '' + convert(varchar(100), @DTU_Storage)
    ELSE 
    SELECT ''Total number of DTUs dominated by resource consumption: '' + convert(varchar(100), @DTU_Perf_99)'
    
    #check if there are any web/biz edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'shared%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "Shared*")
    {
    write-host "`nWeb/Business edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'Shared%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
    
    #check if there are any basic edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'Basic%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "Basic*")
    {
    write-host "`nBasic edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'Basic%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]} 
    }
    
    #check if there are any standard edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'S%' AND slo NOT LIKE 'Shared%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "S*")
    {
    write-host "`nStandard edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'S%' AND slo NOT LIKE 'Shared%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
    
    #check if there are any premium edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'P%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "P*")
    {
    write-host "`nPremium edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'P%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
        

<!---HONumber=AcomDC_0330_2016------>