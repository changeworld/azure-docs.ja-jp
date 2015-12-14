<properties 
    pageTitle="PowerShell を使用した Azure SQL Database のコピーの作成" 
    description="PowerShell を使用した Azure SQL Database のコピーの作成" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="12/01/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# PowerShell を使用した Azure SQL Database のコピーの作成

**1 つのデータベース**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)



次の手順では、PowerShell で SQL データベースをコピーする方法を説明します。このデータベース コピー操作では、[Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx) コマンドレットを利用し、SQL データベースを新しいデータベースにコピーします。コピーは、同じサーバーか別のサーバーで作成するデータベースのスナップショット バックアップです。

> [AZURE.NOTE]Azure SQL Database では、復元できるすべてのユーザー データベースのバックアップが自動的に作成され、保守されます。詳細については、「[ビジネス継続性の概要](sql-database-business-continuity.md)」を参照してください。

コピー プロセスが完了すると、新しいデータベースは、コピー元のデータベースに依存せずに完全に機能するデータベースになります。コピーの完了時点で、新しいデータベースのトランザクションはコピー元のデータベースと同じになります。データベース コピーのサービス レベルとパフォーマンス レベル (価格レベル) はコピー元のデータベースと同じになります。コピーの完了後、コピーは完全に機能する独立したデータベースになります。ログイン、ユーザー、アクセス許可は非依存で管理できます。


データベースを同じ論理サーバーにコピーすると、両方のデータベースで同じログインを利用できます。データベースをコピーするために使用するセキュリティ プリンシパルが、新しいデータベースのデータベース所有者 (DBO) になります。すべてのデータベース ユーザー、アクセス許可、セキュリティ識別子 (SID) がデータベースのコピーにコピーされます。


この記事を完了するには、以下が必要です。

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合、このページの上部の**無料試用版**をクリックしてからこの記事に戻り、最後まで完了してください。
- Azure SQL Database。SQL Database がない場合は、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。
- Azure PowerShell。Azure PowerShell モジュールは、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) を実行してダウンロードおよびインストールすることができます。詳細については、「[Azure PowerShell のインストールと構成の方法](powershell-install-configure.md)」をご覧ください。



## 資格情報を構成してサブスクリプションを選択

まず、Azure アカウントへのアクセスを確立する必要があるため、PowerShell を起動してから以下のコマンドレットを実行します。ログイン画面で、Azure クラシック ポータルへのサインインに使用しているものと同じ電子メールとパスワードを入力します。

	Add-AzureAccount

正常にサインインすると、サインインしている ID や使用中の Azure サブスクリプションを含む情報が画面に表示されます。


### Azure サブスクリプションを選択します。

サブスクリプションを選択するには、サブスクリプション ID またはサブスクリプション名 (**-SubscriptionName**) が必要になります。サブスクリプション ID は前の手順で示された情報からコピーできます。または、複数のサブスクリプションがあり、詳しい情報が必要な場合は、**Get-AzureSubscription** コマンドレットを実行して、結果セットから目的のサブスクリプション情報をコピーできます。ご利用のサブスクリプションを取得したら次のコマンドレットを実行します。

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

**Select-AzureSubscription** を正常に実行すると、PowerShell プロンプトに戻ります。サブスクリプションが複数ある場合は、**Get-AzureSubscription** を実行して、使用するサブスクリプションが **IsCurrent: True** と表示されていることを確認できます。


## 特定の環境用の変数の設定

変数には、例の値を、使用するデータベースとサーバーの特定の値に置き換える必要があるものがいくつかあります。

プレースホルダー値をお使いの環境の値で置き換えます。

    # The name of the server on which the source database resides.
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy). 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server that hosts the target database. This server must be in the same Azure subscription as the source database server. 
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy).
    $PartnerDatabaseName = "partnerDatabaseName" 





## SQL データベースを同じサーバーにコピーする

このコマンドでは、サービスにデータベースのコピー要求を送信します。データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。

    # Copy a database to the same server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerDatabase $PartnerDatabaseName

## SQL データベースを別のサーバーにコピーする

このコマンドでは、サービスにデータベースのコピー要求を送信します。データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    

## コピー操作の進行状況の監視

**Start-AzureSqlDatabaseCopy** の実行後に、コピー要求の状態を確認できます。要求直後にこれを実行すると、通常は、**State : Pending** または **State : Running** が返されます。したがって、出力に **State : COMPLETED** が表示されるまで、これを複数回実行できます。


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName


## SQL Database のコピーの PowerShell スクリプト

    # The name of the server where the source database resides
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy) 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server to host the database copy. This server must be in the same Azure subscription as the source database server
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy)
    $PartnerDatabaseName = "partnerDatabaseName" 


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName "myAzureSubscriptionName"
      
    # Copy a database to a different server (remove the -PartnerServer parameter to copy to the same server)
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    
    # Monitor the status of the copy
    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName
    

## 次のステップ

- [SQL Server Management Studio (SSMS) での接続](sql-database-connect-to-database.md)
- [データベースを BACPAC にエクスポートする](sql-database-export-powershell.md)


## その他のリソース

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [災害復旧訓練](sql-database-disaster-recovery-drills.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1203_2015-->