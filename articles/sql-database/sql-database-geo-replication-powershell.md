<properties 
    pageTitle="PowerShell を使用して Azure SQL Database のアクティブ geo レプリケーションを構成する | Microsoft Azure" 
    description="PowerShell を使用して Azure SQL Database のアクティブ geo レプリケーションを構成する" 
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
    ms.date="04/27/2016"
    ms.author="sstein"/>

# PowerShell を使用して Azure SQL Database の geo レプリケーションを構成する



> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


この記事では、PowerShell を使用して SQL Database の geo レプリケーションを構成する方法を示します。

フェールオーバーを開始する方法については、[Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始](sql-database-geo-replication-failover-powershell.md)に関する記事をご覧ください。

>[AZURE.NOTE] すべてのサービス レベルのすべてのデータベースでアクティブ geo レプリケーション (読み取り可能なセカンダリ) を使用できるようになりました。2017 年 4 月に、読み取り不能なタイプのセカンダリが廃止され、既存の読み取り不能なデータベースは読み取り可能なセカンダリに自動的にアップグレードされます。

同じまたは異なるデータ センターの場所 (リージョン) に最大 4 つの読み取り可能なセカンダリ データベースを構成できます。セカンダリ データベースは、データ センターで障害が発生した場合やプライマリ データベースに接続できない場合に使用できます。

geo レプリケーションを構成するには、次のものが必要です。

- Azure サブスクリプション。Azure サブスクリプションがない場合は、このページの上部にある "**無料アカウント**" をクリックしてサブスクリプションを作成してから、この記事に戻って最後まで完了してください。
- Azure SQL Database データベース。別の地理的リージョンにレプリケートするプライマリ データベースです。
- Azure PowerShell 1.0 以降。Azure PowerShell モジュールをダウンロードしてインストールするには、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。


## 資格情報を構成してサブスクリプションを選択

まず、Azure アカウントへのアクセスを確立する必要があるため、PowerShell を起動してから以下のコマンドレットを実行します。ログイン画面で、Azure ポータルへのサインインに使用しているものと同じ電子メールとパスワードを入力します。


	Login-AzureRmAccount

正常にサインインすると、サインインしている ID や使用中の Azure サブスクリプションを含む情報が画面に表示されます。


### Azure サブスクリプションを選択します。

サブスクリプションを選択するには、サブスクリプション ID が必要です。サブスクリプション ID は前の手順で表示された情報からコピーできます。または、複数のサブスクリプションがあり、詳しい情報が必要な場合は、**Get-AzureRmSubscription** コマンドレットを実行して、結果セットから目的のサブスクリプション情報をコピーできます。次のコマンドレットでサブスクリプション ID を使用し、現在のサブスクリプションを設定します。

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

**Select-AzureRmSubscription** を正常に実行すると、PowerShell プロンプトに戻ります。


## セカンダリ データベースの追加


次の手順では、geo レプリケーション パートナーシップに新しいセカンダリ データベースを作成します。
  
セカンダリ データベースを有効にするには、サブスクリプションの所有者または共同所有者でなければなりません。

**New-AzureRmSqlDatabaseSecondary** コマンドレットを使用すると、パートナー サーバー上のセカンダリ データベースを、接続先のサーバー上のローカル データベース (プライマリ データベース) に追加することができます。

このコマンドレットでは、**Start-AzureSqlDatabaseCopy** を **–IsContinuous** パラメーターに置き換えます。これにより **AzureRmSqlDatabaseSecondary** オブジェクトが出力されます。他のコマンドレットはこのオブジェクトを使用して、特定のレプリケーション リンクを明確に区別することができます。このコマンドレットは、セカンダリ データベースが作成され、そのシード処理が完全に行われると、戻ります。データベースのサイズによって所要時間は異なり、数分 ～ 数時間の幅があります。

セカンダリ サーバー上のレプリケート データベースは、プライマリ サーバー上のデータベースと同じ名前となります。既定でのサービス レベルもプライマリ サーバー上のデータベースの場合と同じになります。セカンダリ データベースは、読み取り可能または読み取り不可とすることができるほか、単一データベースまたはエラスティック データベースとすることができます。詳細については、「[New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx)」 と 「[サービス レベル](sql-database-service-tiers.md)」を参照してください。セカンダリ データベースを作成しシード処理を行うと、プライマリ データベースから新しいセカンダリ データベースへのデータのレプリケートが開始されます。次の手順では、PowerShell を使用してこの操作を行い、単一のデータベースまたはエラスティック データベースとして読み取り不可または読み取り可能なセカンダリ データベースを作成する方法を説明します。

パートナー データベースが既に存在する場合 (たとえば、前の geo レプリケーションのリレーションシップを終了した結果として)、コマンドは失敗します。



### 読み取り不可のセカンダリ データベース (単一のデータベース) の追加

次のコマンドでは、リソース グループ "rg2" に属するサーバー "srv2" のデータベース "mydb" に対して読み取り不可のセカンダリ データベースが作成されます。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### 読み取り可能なセカンダリ (単一データベース) の追加

次のコマンドでは、リソース グループ "rg2" に属するサーバー "srv2" のデータベース "mydb" に対して読み取り可能なセカンダリ データベースが作成されます。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### 読み取り不可のセカンダリ データベース (エラスティック データベース) の追加

次のコマンドでは、リソース グループ "rg2" に属するサーバー "srv2" のエラスティック データベース プール "ElasticPool1" 内のデータベース "mydb" に対して読み取り不可のセカンダリ データベースが作成されます。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### 読み取り可能なセカンダリ データベース (エラスティック データベース) の追加

次のコマンドでは、リソース グループ "rg2" に属するサーバー "srv2" のエラスティック データベース プール "ElasticPool1" 内のデータベース "mydb" に対して読み取り可能なセカンダリ データベースが作成されます。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## セカンダリ データベースを削除する

セカンダリ データベースとそのプライマリ データベースとの間のレプリケーション パートナーシップを完全に終了させるには、**Remove-AzureRmSqlDatabaseSecondary** コマンドレットを使用します。リレーションシップの終了後、セカンダリ データベースは読み取り/書き込みデータベースになります。セカンダリ データベースへの接続が切断された場合、コマンドは成功します。ただし、接続が復元されると、セカンダリ データベースは読み取り/書き込みデータベースになります。詳細については、「[Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx)」 と 「[サービス レベル](sql-database-service-tiers.md)」を参照してください。

このコマンドレットは、レプリケーション用の Stop-AzureSqlDatabaseCopy に取って代わります。

この削除は強制終了の場合と同じです。強制終了では、レプリケーション リンクが削除され、前のセカンダリ データベースは、終了前に完全にレプリケートされるとは限らないスタンドアロン データベースとして残されます。前のプライマリ データベースと前のセカンダリ データベースが利用可能な場合は、その両方ですべてのリンク データがクリーンアップされます。このコマンドレットは、レプリケーション リンクが削除されると戻ります。


セカンダリ データベースを削除するユーザーは、RBAC に従ってプライマリ データベースとセカンダリ データベースの両方への書き込みアクセス権を有する必要があります。詳細については、ロールベースのアクセス制御を参照してください。

次のコードを実行すると、"mydb" という名前のデータベースと、リソース グループ "rg2" に属するサーバー "srv2" とのレプリケーション リンクが削除されます。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## geo レプリケーションの構成と正常性を監視する

監視タスクには、geo レプリケーションの構成に関する監視と、データ レプリケーションの正常性に関する監視が含まれます。

sys.geo\_replication\_links のカタログ ビューに表示される順方向のレプリケーション リンクに関する情報を取得するには、[Get AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) を使用します。

次のコマンドでは、リソース グループ "rg2" に属するサーバー "srv2” 上のプライマリ データベース "mydb" とセカンダリ データベースの間のレプリケーション リンクのステータスを取得します。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


  

## 次のステップ

- [Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始](sql-database-geo-replication-failover-powershell.md)
- [災害復旧訓練](sql-database-disaster-recovery-drills.md)




## その他のリソース

- [geo レプリケーションのセキュリティ構成](sql-database-geo-replication-security-config.md)
- [新しい geo レプリケーション機能に関するスポットライト](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [SQL Database BCDR の FAQ](sql-database-bcdr-faq.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)
- [クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md)

<!---HONumber=AcomDC_0608_2016-->