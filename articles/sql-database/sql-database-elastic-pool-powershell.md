<properties 
   pageTitle="PowerShell を使用したSQL Database のエラスティック データベース プールの作成と管理" 
   description="PowerShell を使用した SQL Database のエラスティック データベースの作成と管理" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="09/23/2015"
   ms.author="adamkr; sstein"/>

# PowerShell を使用したSQL Database のエラスティック データベース プールの作成と管理

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-client-library.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


## 概要

この記事では、PowerShell を使用して、SQL Database のエラスティック データベース プールを作成し、管理する方法を説明します。


Azure PowerShell のエラスティック データベース プールを作成するための手順は、わかりやすさを重視し、細かく分けて説明しています。コマンドの正確な一覧のみが必要な方は、この記事下部の**まとめ**セクションをご覧ください。

この記事では、Azure サブスクリプションを除き、エラスティック データベース プールを作成して構成するために必要なすべてを作成する方法を示します。Azure サブスクリプションをお持ちでない場合、このページの上部の**無料試用版**をクリックしてからこの記事に戻り、最後まで完了してください。

> [AZURE.NOTE]エラスティック データベース プールは現在プレビュー段階であり、SQL Database V12 サーバーでのみ使用できます。


## 前提条件

PowerShell でエラスティック データベース プールを作成するには、Azure PowerShell がインストールされ実行されていること、リソース マネージャー モードに切り替えて Azure リソース マネージャーの PowerShell コマンドレットを使えるようにしておくことが必要です。

Azure PowerShell モジュールは、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) を実行してダウンロードおよびインストールすることができます。詳細については、「[Azure PowerShell のインストールと構成の方法](powershell-install-configure.md)」をご覧ください。

Azure SQL Database とエラスティック データベース プールの作成と管理に使うコマンドレットは、Azure リソース マネージャー モジュールにあります。Azure PowerShell を開始するときに、Azure モジュールのコマンドレットが既定でインポートされます。Azure リソース マネージャー モジュールに切り替えるには、Switch-AzureMode コマンドレットを使用します。

	Switch-AzureMode -Name AzureResourceManager

詳細については、「[リソース マネージャーでの Windows PowerShell の使用](powershell-azure-resource-manager.md)」をご覧ください。


## 資格情報を構成してサブスクリプションを選択

これで、Azure リソース マネージャー モジュールが実行され、エラスティック データベース プールを作成して構成するために必要なすべてのコマンドレットにアクセスできるようになりました。はじめに Azure アカウントへのアクセスを確立する必要があります。次を実行すると資格情報を入力するサインイン画面が表示されます。Azure ポータルへのサインインに使用しているものと同じ電子メールとパスワードを使用します。

	Add-AzureAccount

正常にサインインすると、サインインしている ID や使用中の Azure サブスクリプションを含む情報が画面に表示されます。


### Azure サブスクリプションを選択します。

サブスクリプションを選択するには、サブスクリプション ID とサブスクリプション名 (**-SubscriptionName**) が必要になります。前の手順からコピーするか、複数のサブスクリプションがある場合は **Get-AzureSubscription** コマンドレットを実行して結果セットから目的のサブスクリプション情報をコピーできます。ご利用のサブスクリプションを取得したら次のコマンドレットを実行します。

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## リソース グループ、サーバー、ファイアウォール規則の作成

これでご利用の Azure サブスクリプションに対してコマンドレットを実行する準備ができましたので、次にエラスティック データベース プールを作成するサーバーを含むリソース グループを確立します。次のコマンドを編集して選択した任意の有効な場所で使用できます。**(Get-AzureLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** を実行して有効な場所の一覧を取得します。

すでにリソース グループがある場合は次の手順に進みます。次のコマンドを実行して新しいリソース グループを作ることもできます。

	New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"

### サーバーの作成 

エラスティック データベース プールは Azure SQL Database サーバーの内部で作成されます。すでにサーバーがある場合は次の手順に進みます。次のコマンドを実行して新しい V12 サーバーを作ることもできます。ServerName をご利用のサーバー名に置き換えます。サーバー名がすでに使われている場合はエラーが発生する可能性があるため、Azure SQL Server で一意のサーバー名を使用する必要があります。このコマンドは完了するまでに数分かかる場合があることに注意してください。サーバーが正常に作成されると、サーバーの詳細と PowerShell のプロンプトが表示されます。コマンドを編集して選択した任意の有効な場所で使用できます。

	New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

コマンドを実行すると、**[ユーザー名]** と **[パスワード]** の入力を求めるウィンドウが表示されます。ここで入力するユーザー名とパスワードは Azure の資格情報ではなく、新しいサーバーの管理者の資格情報となります


### サーバーのファイアウォール規則を構成し、サーバーへのアクセスを許可します。

ファイアウォール規則を確立してサーバーにアクセスします。ご利用のコンピューターで有効な開始 IP アドレスと終了 IP アドレスに置き換え、次のコマンドを実行します。

サーバーで別の Azure サービスへのアクセス許可が必要な場合は、**- AllowAllAzureIPs** スイッチを追加して特別なファイアウォール規則を追加し、サーバーへのすべての Azure トラフィック アクセスを許可します。

	New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

詳細については、「[Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx)」をご覧ください。


## エラスティック データベース プールとエラスティック データベースの作成

これでリソース グループ、サーバー、ファイアウォール規則が構成され、サーバーにアクセスできるようになりました。次のコマンドでエラスティック データベース プールを作成できます。このコマンドで合計 400 eDTU を共有するプールが作成されます。プールの各データベースは常に 10 eDTU 使用できることが保証されます (DatabaseDtuMin)。プールの各データベースは最大 100 eDTU を使用できます (DatabaseDtuMax)。パラメーターの詳細については「[SQL Database のエラスティック プール (プレビュー)](sql-database-elastic-pool.md)」をご覧ください。


	New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### エラスティック データベースを作成するか、エラスティック データベースに追加する

前の手順で作成したプールはまだ空であり、エラスティック データベースが含まれていません。このセクションでは、プール内で新しいエラスティック データベース作成する方法と、既存のデータベースをプールに追加する方法を示します。

*プールを作成したら、Transact-SQL を使用してプールに新しいエラスティック データベースを作成したり、既存のデータベースをプールの内外に移動したりできます。詳細については、「[エラスティック データベース プールのリファレンス - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL)」をご覧ください。*

### エラスティック データベース プールでの新しい エラスティック データベースの作成

プールに直接新しいデータベースを作成するには、**New-azuresqldatabase** コマンドレットを使用して **ElasticPoolName** パラメーターを設定します。


	New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### 既存のデータベースをエラスティック データベース プールに移動

既存のデータベースをプールに移動するには、**Set-AzureSqlDatabase** コマンドレットを使用して **ElasticPoolName** パラメーターを設定します。


デモでは、エラスティック データベース プールに含まれていないデータベースを作成します。

	New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

エラスティック データベース プールで既存のデータベースに移動します。

	Set-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## エラスティック データベース プールのパフォーマンス設定の変更


    Set-AzureSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## エラスティック データベースとエラスティック データベース プールの監視

### エラスティック データベース プールの操作の状態の取得

作成や更新など、エラスティック データベース プール操作の状態を確認できます。

	Get-AzureSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### エラスティック データベースをエラスティック データベース プールに出し入れする際の状態の取得

	Get-AzureSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### エラスティック データベース プールのリソース消費メトリックの取得

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

	$metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

呼び出しを繰り返し、次のようなデータを追加することで日数を追加できます。

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
テーブルの書式を設定します。

    $table = Format-MetricsAsTable $metrics 

CSV ファイルにエクスポートします。

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

### エラスティック データベースのリソース消費メトリックの取得

これらの API は、次のセマンティックな相違点を除き、スタンドアロン データベースのリソース使用率の監視に使用する現在の (V12) API と同じです。

* 取得されたこの API メトリックは、エラスティック データベース プールの databaseDtuMax (または CPU、IO など基盤となるメトリックと同等の上限) セットのパーセンテージとして表されます。たとえば、これらのメトリックいずれかの使用率が 50% であることは、特定のリソース消費量が、DB の上限の 50% (親のエラスティック データベース プールで該当するリソースの制限) であることを示しています。 

メトリックは次のようにして取得します。$metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

呼び出しを繰り返し、次のようなデータを追加することで、必要に応じて日数を追加できます。

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

テーブルの書式を設定します。

    $table = Format-MetricsAsTable $metrics 

CSV ファイルにエクスポートします。

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## まとめ


    Switch-AzureMode -Name AzureResourceManager
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000
    New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"
    New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"
    New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"
    New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
    New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1" -MaxSizeBytes 10GB
    Set-AzureSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## 次のステップ

エラスティック データベース プールを作成した後は、エラスティック ジョブを作成してプール内のエラスティック データベース を管理できます。エラスティック ジョブはプールのデータベースの任意の数に対して T-SQL スクリプトの実行を容易にします。詳細については、「[弾力性データベース ジョブの概要](sql-database-elastic-jobs-overview.md)」をご覧ください。


## エラスティック データベースのリファレンス

API とエラーの詳細を含む弾力性データベースと弾力性データベース プールの詳細については、「[弾力性データベースのリファレンス](sql-database-elastic-pool-reference.md)」をご覧ください。

<!---HONumber=Oct15_HO1-->