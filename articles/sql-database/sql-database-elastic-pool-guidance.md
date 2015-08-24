<properties 
	pageTitle="Azure SQL Database エラスティック データベース プールの価格およびパフォーマンスに関する考慮事項" 
	description="弾力性データベース プールは、弾力性データベースのグループで共有される使用可能なリソースのコレクションです。このドキュメントは、データベースのグループに対して、エラスティック データベース プールを使用することが適切であるか評価するガイダンスです。" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="08/12/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# エラスティック データベース プールの価格およびパフォーマンスに関する考慮事項


このドキュメントは、データベース グループにエラスティック データベース プールを使用することが、データベースの使用パターン、およびエラスティック データベース プールと Single Database 間の価格差に基づき、コスト効率がよいかを評価するためのガイダンスとなるものです。既存の SQL データベースのセットに必要な現在のプール サイズを決定するためのガイダンスも含まれています。

- エラスティック データベース プールの概要については、「[SQL Database のエラスティック データベース プール](sql-database-elastic-pool.md)」を参照してください。
- エラスティック データベース プールの詳細については、「[SQL Database のエラスティック データベース プールのリファレンス](sql-database-elastic-pool-reference.md)」を参照してください。


> [AZURE.NOTE]エラスティック データベース プールは現在プレビュー段階であり、SQL Database V12 サーバーでのみ使用できます。

## エラスティック データベース プール

SaaS ISV は、複数のデータベースで構成される大規模なデータ層の上にアプリケーションを構築します。一般的なアプリケーションでは、これらのデータベースは、別のお客様に独自の異なる予測困難な使用パターンで使用されます。ISV が各データベースのリソース要件を個別に予測するのは困難です。ISV はこのような状況下で、すべてのデータベースのスループットおよび応答時間をよくするために、かなりのコストを費やしリソースを過剰にプロビジョニングしてしまう場合があります。または ISV があまりコストをかけず、お客様にパフォーマンスの低下のリスクを与えてしまう場合があります。

Azure SQL データベースのエラスティック データベース プールを使用すると、SaaS ISV は、各データベースのパフォーマンスに弾力性を提供しながら、データベース グループの価格に対するパフォーマンスを所定の予算内で最適化できます。ISV は、エラスティック データベース プールを使用して、複数のデータベースで共有されるエラスティック データベース スループット ユニット (eDTU) を購入して、予測が難しい個々のデータベースの使用量を調整できます。プールの eDTU 要件は、そのデータベースの使用量の合計によって決まります。プールに使用可能な eDTU の量は、ISV の予算に左右されます。ISV は、エラスティック データベース プールを使用すると、予算 (または逆にそのプール) に対するパフォーマンスを簡単に決定できます。ISV は、単純にプールにデータベースを追加し、データベースに必要な eDTU 保証または上限を設定し、予算に基づいてプールの eDTU を設定します。ISV はエラスティック プールを使用して、リーン スタートアップから成熟企業までの増え続けるサービスをシームレスに拡張できます。
  


## どのようなときにエラスティック データベース プールを検討するか

エラスティック データベース プールは、多数のデータベースが特定の使用パターンで使用されている場合に適しています。あるデータベースは、使用が急増することはあまりなく、平均使用量が低いパターンの特徴を持っています。

プールに追加できるデータベース数が多いほど、多く節約できますが、アプリケーションの使用パターンによっては、わずか 4 つの S3 データベースで節約ができる場合があります。

以下のセクションは、使用しているデータベースのコレクションでエラスティック データベース プールを使用した場合、メリットがあるかどうかを評価する助けとなります。この例では、Standard エラスティック データベース プールを使用していますが、同じ原則は Basic プールと Premium プールにも適用されます。

### データベースの使用パターンの評価

次の図では、アイドル時間は長いが、定期的にアクティビティが急増するデータベースを示しています。このような使用パターンは、エラスティック データベース プールに適しています。
 
   ![1 つのデータベース][1]

上図の DB1 の 1 時間のピークは 90 DTU ですが、全体的な平均使用量は 5 DTU 未満です。このワークロードを Single Database で実行するには、S3 のパフォーマンス レベルが必要です。ただし、これではアクティビティが少ない期間、リソースはほとんど使用されません。

エラスティック データベース プールでは、これらの未使用の DTU を複数のデータベースで共有できるので、必要な DTU の総量と全体的なコストを削減できます。

DB1 と類似する使用パターンを持つデータベースが他にもあると仮定し、前の例に追加しています。以下の次の 2 つの図では、時間経過があっても使用の重複がない 4 つのデータベースと 20 のデータベースの使用が同じグラフに重ねられています。

   ![4 つのデータベース][2]

   ![20 のデータベース][3]

全 20 のデータベースの DTU 使用量合計は、上図では黒線で示されています。これは、DTU の合計使用量が 100 DTU を超えることはなく、20 のデータベースでこの期間にわたって 100 eDTU を共有することが可能であることを示しています。これは、Single Database のそれぞれのパフォーマンス レベルが S3 であるときと比較した場合、DTU を 20 倍削減し価格を 6 倍下げます。


次の理由からこの例は理想的です。

- 各データベースのピーク時の使用量と、平均使用量に大きな差があります。  
- 各データベースのピーク使用時間が異なります。 
- eDTU が多数のデータベースで共有されています。


エラスティック データベース プールの価格は、プール eDTU とその中のデータベース数の相互作用によります。プールの GA 価格の eDTU 単価は Single Database の DTU 単価の 3 倍ですが、**プール eDTU は、多数のデータベースで共有できるので、多くの場合合計 eDTU は少なくて済みます**。これらの価格と eDTU 共有の特徴が、プールで節約を可能にするベースとなります。

<br>

データベース数とデータベースの使用量に関連する次の経験則は、エラスティック データベース プールが Single Database でのパフォーマンス レベルを使用した場合と比較し、コストの節約になることを保証します。このガイダンスは、一般提供 (GA) 価格に基づきます。なお、GA 価格は、プレビュー中は 50% 割り引かれます。したがって、これらの経験則は、比較的控え目に考慮してください。


### 最小数のデータベース

3 つ以上のデータベースで 1 eDTU を共有できる場合、GA 価格のエラスティック データベース プールは、パフォーマンス上非常にコスト効率の高い選択肢となります。つまり、Single Database のパフォーマンス レベルの DTU の合計は、プールの eDTU の 3 倍以上になります。可能なサイズの詳細については、「[エラスティック データベース プールとエラスティック データベースの eDTU と記憶域の上限](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases)」を参照してください。


***例***<br> 100 eDTU のエラスティック データベース プールが Single Database のパフォーマンス レベルを使用した場合と比較しコスト効率が高くなるには、最低 4 つの S3 データベースまたは最低 36 の S0 データベースが必要です。(プレビュー価格の場合、データベース数に基づく価格分岐点は 2 つの S3 データベースまたは 17 の SO データベースに減ります。)



### 同時にピークとなるデータベースの最大数

eDTU を共有する場合は、プール内のすべてのデータベースが、単一のデータベースのパフォーマンス レベルを使用している場合の eDTU を上限まで同時に使用できるわけではありません。同時にピークになるデータベース数が少ないほど、プール eDTU を低く設定してよりコスト効率を高めることができます。一般的に、その eDTU の上限まで同時にピークとなることができるのは、プールの 1/3 以下のデータベースです。

***例***<br> 200 eDTU のプール内の 4 つの S3 データベースのコストを削減するには、これらのデータベースの最大 2 つのみ同時にピークになることが可能です。これら 4 つの S3 データベースの 3 つ以上が同時にピークとなる場合、プール サイズを 200 eDTU よりも大きくする必要があります。プール サイズを 200 eDTU よりも大きく変更した場合、Single Database のパフォーマンス レベルを使用した場合よりもコストを低く抑えるには、プールの S3 データベース数を増やす必要があります。


この例では、プール内の他のデータベースの使用は考慮されていないことに注意してください。すべてのデータベースがある特定の時間にいくらか使用されている場合、同時にピークとなることができるのは、1/3 未満のデータベースです。


### 各データベースの DTU の使用量

データベースのピーク時と平均使用時の差が大きいということは、使用量が低い時間が長く、使用量が高い時間が短いことを示します。使用量がこのようなパターンになっている場合、リソースを複数のデータベースで共有するのが適しています。データベースのピーク時の使用量が平均使用量よりも約 3 倍多い場合、プールを使用することを検討した方がよいでしょう。

    
***例***<br> S3 データベースが、ピーク時には 100 DTU を使用し、平均では 30 DTU 以下を使用する場合、eDTU をエラスティック データベース プールで共有するのが適しています。または、S1 データベースがピーク時には 20 DTU を使用し、平均では 7 DTU 未満を使用する場合、エラスティック データベース プールが適しています。
    

## エラスティック データベース プールと Single Database の場合との価格差の比較のヒューリスティック 

Single Database を個々に使用するのと、エラスティック データベース プールを使用する場合のコスト効率性を評価するには、次のヒューリスティックが役立ちます。

1. プールに必要な eDTU は、次のように評価します。
    
    MAX(*DB の合計数* * *DB ごとの平均 DTU 使用量*、*同時にピークとなる DB 数* * *DB ごとの DTU 使用量のピーク*)

2. 手順 1 の評価よりも大きい、プール用の使用可能な最小の eDTU 値を選択します。使用可能な eDTU を選択する場合には、「[エラスティック データベース プールとエラスティック データベースの DTU と記憶域の上限](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases)」に示す eDTU の有効値を参照してください。


3. 次のようにプール価格を計算します。

    プールの価格 = (*プールの eDTU* * *プールの eDTU 単価*) + (*DB の合計数* * *プールの DB 単価*)

    料金情報については、「[SQL Database の料金](http://azure.microsoft.com/pricing/details/sql-database/)」を参照してください。


4. 手順 3 のプールの価格と、Single Database の適切なパフォーマンス レベルを使用した場合の価格を比較してください。



## 既存の SQL データベース用の最適なプール eDTU のサイズの決定 

エラスティック データベース プールに最適なサイズは、eDTU の合計とプール内のすべてのデータベースに必要な記憶域リソースによります。これには、次の 2 つの数値のうちの大きい方を決定する必要があります。

* プール内のすべてのデータベースに使用される DTU の最大値。
* プール内のすべてのデータベースに使用される記憶域の最大バイト数。 

Standard サービス階層では、プールの 1 eDTU の構成ごとに 1 GB の記憶域が許可されていることに注意してください。たとえば、プールが 200 DTU で構成されている場合、記憶域の上限は 200 GB になります。

次の表は、各価格レベルの eDTU あたりの記憶域サイズを示しています。

| レベル | eDTU | Storage |
| :--- | :--- | :--- |
| 基本 | 1 | 100 MB |
| Standard | 1 | 1 GB |
| Premium | 1 | 0\.5 GB |


### 推奨サイズを決定するためのサービス層アドバイザー (STA) と動的管理ビュー (DMV) の使用   

STA および DMV には、エラスティック データベース プールのサイズを決定するためのさまざまなツール オプションと機能があります。使用しているツール オプションにかかわらず、サイズの評価は、初期評価とエラスティック データベース プールの作成のみに使用される必要があります。プールを作成したら、リソースの使用量を正確に監視し、必要に応じてプールのパフォーマンスの設定を上下に調整します。

**STA**<br>STA は、既存の SQL Database サーバー内のデータベースのリソースの使用量履歴を自動的に評価する[プレビュー ポータル](https://portal.azure.com)の組み込みのツールで、エラスティック データベース プールを適正に構成するための推奨を行います。詳細については、「[エラスティック データベース プールの価格レベルの提案](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations)」を参照してください。

**DMV サイズ変更ツール**<br>DMV サイズ変更ツールは、PowerShell スクリプトとして提供されており、サーバー内の既存のデータベースのエラスティック データベース プール内でのサイズ見積もりをカスタマイズできます。

### STA および DMV ツールのどちらを選択するか 

アプリケーションの分析に、適切なツールを選択する必要があります。次のテーブルにこれら 2 のツールの違いを示します。

| 機能 | STA | DMV |
| :--- | :--- | :--- |
| 分析で使用するデータ サンプルの精度です。 | 15 秒 | 15 秒 |
| プールの場合と Single Database のパフォーマンス レベルの場合の価格差を考慮します。 | あり | いいえ |
| サーバー内で分析されるデータベース一覧をカスタマイズできます。 | いいえ | あり |
| 分析で使用される期間をカスタマイズできます。 | いいえ | あり |


### STA を使用したエラスティック プールのサイズ評価  

STA は、データベースの使用量の履歴を評価して、Single Database のパフォーマンス レベルを使用するよりもコスト効果が高い場合は、エラスティック データベース プールを推奨します。プールが推奨される場合、ツールは推奨されるデータベース一覧、プール eDTU の推奨量、各エラスティック データベースの最小/最大 eDTU 設定を提供します。データベースがプールの候補と見なされるには 7 日間以上の存在している必要があります。

既存のサーバーにエラスティック データベース プールを追加する場合、STA はプレビュー ポータルで使用可能です。そのサーバーがエラスティック データベース プールに推奨されている場合は、[エラスティック データベース プール] 作成ページに表示されます。お客様はいつでも推奨の構成を変更して、独自のエラスティック データベース プール グループを作成できます。

詳細については、「[エラスティック データベース プールの価格レベルの提案](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations)」を参照してください。

### 動的管理ビュー (DMV) を使用したエラスティック プールサイズの評価 

[Sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV は、個々のデータベースのリソース使用量を測定します。この DMV は、データベースのパフォーマンス レベルに対する制限の割合として CPU、IO、ログ、およびデータベースのログ使用量を提示します。このデータは、データベースの DTU 使用量を任意の15 秒間隔での計算するために使用できます。

エラスティック データベース プールの 15 秒間隔での eDTU の合計使用量は、その時間のすべての候補データベースの eDTU 使用量を集計することによって推定できます。パフォーマンス目標によっては、わずかなサンプル データの割合は破棄した方が理にかなっている場合があります。たとえば、すべての時間間隔の合計 eDTU の 99% のパーセンタイル値を適用して異常値を排除して、サンプル化された時間間隔の 99% のエラスティック データベース プール eDTU を提供するようにできます。

## データベースの合計 eDTU 使用量を評価するための PowerShell スクリプト

ここでは、SQL Database サーバーのユーザー データベースの合計 eDTU 値を評価するため PowerShell スクリプトのサンプルを示します。

スクリプトは実行中にのみデータを収集します。スクリプトは、運用時の一般的なワークロードに対しては、最低 1 日間実行する必要があります。1 週間以上の方がより正確な評価が可能でしょう。データベースに対する典型的なワークロードがある期間、スクリプトを実行します。

> [AZURE.IMPORTANT]スクリプトの実行中は、PowerShell ウィンドウを開いたままにする必要があります。通常時およびピーク時の両方にわたる典型的なワークロードを示すデータが十分にキャプチャされ、スクリプトが十分な時間を実行されるまで、PowerShell ウィンドウは閉じないようにしてください。

### スクリプトの前提条件 

スクリプトの実行前に、次をインストールしてください。

- 最新の [Powershell コマンド ライン ツール](http://go.microsoft.com/?linkid=9811175&clcid=0x409)。
- [SQL Server 2014 Feature Pack](https://www.microsoft.com/download/details.aspx?id=42295)。


### スクリプトの詳細


スクリプトは、ローカル コンピューターまたはクラウド上の仮想マシンから実行できます。ローカル コンピューターから実行する場合、スクリプトは、ターゲット データベースからデータをダウンロードする必要があるためデータ送信費用が発生する可能性があります。次に、ターゲット データベースの数と、スクリプトの実行期間に基づくデータ量の評価を示します。Azure のデータ転送コストの詳細については、「[データ転送の料金詳細](http://azure.microsoft.com/pricing/details/data-transfers/)」を参照してください。
       
 -     1 データベース 1 時間あたり = 38 KB
 -     1 データベース 1 日あたり = 900 KB
 -     1 データベース 1 週間あたり = 6 MB
 -     100 データベース 1 日あたり = 90 MB
 -     500 データベース 1 週間あたり = 3 GB

このスクリプトは、現在の Standard のエラスティック プール層でのパブリック プレビューの候補として適切ではない特定のデータベースは除外します。ターゲット サーバーからその他のデータベースを除外する必要がある場合、スクリプトを変更して条件に一致させる必要があります。既定では、スクリプトは次の情報はコンパイルしません。

* エラスティック データベース (エラスティック プール内に既にあるデータベース)。
* サーバーの master データベース。

スクリプトには、分析用の中間データを格納する出力用のデータベースが必要です。新規または既存のデータベースを使用できます。ツールの実行には技術的に不要ですが、分析結果への影響を回避するために、出力データベースは別のサーバーに配置する必要があります。出力データベースのパフォーマンス レベルは最低 S0 以上にする必要があります。大量のデータベースのデータを長時間収集する場合、出力データベースのパフォーマンス レベルを高いものにアップグレードすることを検討するとよいでしょう。

スクリプトには、ターゲット サーバー (エラスティック データベース プールの候補) に接続するための "abcdef.database.windows.net" などのような完全なサーバー名の資格情報を指定する必要があります。現在、スクリプトでは一度に複数のサーバーを分析できません。


初期パラメーター値のセットの送信後には、Azure アカウントにログオンするように求められます。これは出力データベース サーバーではなくターゲット サーバーにログオンするためのものです。
	
スクリプトの実行時、次の警告が示された場合は無視してかまいません。

- 警告: Switch-AzureMode コマンドレットは廃止予定です。
- 警告: SQL Server サービスの情報を取得できませんでした。'Microsoft.Azure.Commands.Sql.dll' 上の WMI に接続しようとすると、「RPC サーバーを利用できません。」というエラーで失敗します。

スクリプトが完了すると、ターゲット サーバー内のすべての候補データベースを格納するために、エラスティック プールに必要な推定 eDTU 数が出力されます。この推定 eDTU は、これらのデータベースを格納するエラスティック データベース プールを作成および構成するため使用できます。プールが作成され、プールにデータベースを移動した後には、数日間厳密に監視する必要があります。そして必要に応じて、プール eDTU 構成に必要な調整を行う必要があります。


スクリプト全体をコピーする場合、スクリプト内の任意のテキストを 3 回クリック (トリプルクリック) します。

    
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
        

## 概要

すべての Single Database がエラスティック データベース プールに適してはいません。平均使用量が少なく、使用の急上昇があまりない特徴のデータベースが、エラスティック データベース プールに適した候補です。アプリケーションの使用パターンは動的なので、この記事で説明した情報およびツールを使用して、使用しているデータベースの一部または全部にエラスティック データベース プールが適しているか初期評価を行ってください。この記事は、エラスティック プールが適しているか判断するための開始点にすぎません。リソースの使用状況の履歴は (STA または DMV を使用して) 継続的に監視し、すべてのデータベースのパフォーマンス レベルを常に再評価する必要があることに注意してください。データベースは簡単にエラスティック データベース プールから出し入れできます。また、データベースが大量にある場合、データベースを割り振ることのできるさまざまなサイズのプールを複数持つことが可能であることを念頭に置いてください。



<!--Image references-->
[1]: ./media/sql-database-elastic-pool-guidance/one-database.png
[2]: ./media/sql-database-elastic-pool-guidance/four-databases.png
[3]: ./media/sql-database-elastic-pool-guidance/twenty-databases.png

<!---HONumber=August15_HO7-->