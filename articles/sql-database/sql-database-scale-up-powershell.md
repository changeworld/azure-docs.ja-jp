<properties 
    pageTitle="PowerShell を使用して Azure SQL Database のサービス階層とパフォーマンス レベルを変更する" 
    description="「Azure SQL Database のサービス階層とパフォーマンス レベルを変更する」では、PowerShell を使用した SQL Database のスケール アップとスケール ダウンの方法について説明しています。PowerShell を使用して Azure SQL Database の価格レベルを変更します。" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/29/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# PowerShell で SQL Database のサービス階層とパフォーマンス レベル (価格レベル) を変更する


> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


サービス レベルとパフォーマンス レベルは、SQL Database で利用できる機能とリソースを表しており、アプリケーションのニーズの変化に応じて更新できます。詳細については、「[サービス レベル](sql-database-service-tiers.md)」をご覧ください。

データベースのサービス レベルやパフォーマンス レベルを変更すると、新しいパフォーマンス レベルで元のデータベースのレプリカが作成され、接続先がそのレプリカに切り替えられます。このプロセスでデータが失われることはありませんが、レプリカに切り替えるほんの少しの間、データベースに接続できなくなるため、実行中の一部トランザクションがロールバックされる場合があります。この時間はさまざまですが、平均 4 秒以内であり、99% 以上が 30 秒未満です。ごくまれですが、特に、接続できなくなった時点で多数のトランザクションが実行中の場合、この時間が長引くことがあります。

スケールアップ プロセス全体の継続時間は、変更前後のデータベースのサイズとサービス レベルによって異なります。たとえば、250 GB のデータベースを Standard サービス レベルとの間または Standard サービス レベル内で変更する場合は、6 時間以内に完了します。Premium サービス レベル内で同じサイズのデータベースのパフォーマンス レベルを変更する場合は、3 時間以内で完了します。


- データベースをダウングレードするには、データベースがダウングレード後のサービス階層で許可されている最大サイズより小さい必要があります。 
- [geo レプリケーション](sql-database-geo-replication-portal)を有効にしてデータベースをアップグレードする場合、まず、そのセカンダリ データベースを目的のパフォーマンス レベルにアップグレードしてから、プライマリ データベースをアップグレードする必要があります。
- Premium サービス階層からダウングレードするときは、最初に geo レプリケーション リレーションシップをすべて終了する必要があります。「[Azue SQL Database を障害から回復する](sql-database-disaster-recovery.md)」に記載されている手順に従って、プライマリ データベースとアクティブなセカンダリ データベース間のレプリケーション プロセスを停止できます。
- サービス階層によって、提供されている復元サービスは異なります。ダウングレードすると、特定の時点に復元する機能を使えなくなったり、バックアップの保存期間が短くなったりする可能性があります。詳細については、「[Azure SQL Database のバックアップと復元](sql-database-business-continuity.md)」を参照してください。
- データベースに対する新しいプロパティは、変更が完了するまで適用されません。



**この記事を完了するには、以下が必要です。**

- Azure サブスクリプション。Azure サブスクリプションがない場合は、このページの上部にある "**無料アカウント**" をクリックしてサブスクリプションを作成してから、この記事に戻って最後まで完了してください。
- Azure SQL Database。SQL Database がない場合は、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。
- Azure PowerShell。


PowerShell コマンドレットを実行するには、Azure PowerShell をインストールし、実行している必要があります。詳細については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」をご覧ください。



## 資格情報を構成してサブスクリプションを選択

まず、Azure アカウントへのアクセスを確立する必要があるため、PowerShell を起動してから以下のコマンドレットを実行します。ログイン画面で、Azure ポータルへのサインインに使用しているものと同じ電子メールとパスワードを入力します。

	Login-AzureRmAccount

正常にサインインすると、サインインしている ID や使用中の Azure サブスクリプションを含む情報が画面に表示されます。


### Azure サブスクリプションを選択します。

サブスクリプションを選択するには、サブスクリプション ID またはサブスクリプション名 (**-SubscriptionName**) が必要になります。サブスクリプション ID は前の手順で示された情報からコピーできます。または、複数のサブスクリプションがあり、詳しい情報が必要な場合は、**Get-AzureSubscription** コマンドレットを実行して、結果セットから目的のサブスクリプション情報をコピーできます。ご利用のサブスクリプションを取得したら次のコマンドレットを実行します。

	$SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId




## SQL Database のサービス階層とパフォーマンス レベルを変更する

**Set-AzureRmSqlDatabase** コマンドレットを実行し、**-RequestedServiceObjectiveName** を目的の価格レベルのパフォーマンス レベルに設定します (*S0*、*S1*、*S2*、*S3*、*P1*、*P2* など)。

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"

    $NewEdition = "Standard"
    $NewPricingTier = "S2"

    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier


  

   


## SQL Database のサービス階層とパフォーマンス レベルを変更するサンプル PowerShell スクリプト

    

    
    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest
    
        


## 次のステップ

- [スケールアウトとスケールイン](sql-database-elastic-scale-get-started.md)
- [SSMS での SQL Database の接続とクエリ](sql-database-connect-query-ssms.md)
- [Azure SQL Database のエクスポート](sql-database-export-powershell.md)

## その他のリソース

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database のドキュメント](http://azure.microsoft.com/documentation/services/sql-database/)
- [Azure SQL Database コマンドレット](http://msdn.microsoft.com/library/mt574084.aspx)

<!---HONumber=AcomDC_0330_2016------>