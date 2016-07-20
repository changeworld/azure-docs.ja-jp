<properties 
    pageTitle="PowerShell で BACPAC ファイルをインポートして新しい Azure SQL Database を作成する | Microsoft Azure" 
    description="PowerShell で BACPAC ファイルをインポートして新しい Azure SQL Database を作成する" 
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
    ms.date="07/06/2016"
    ms.author="sstein"/>

# PowerShell で BACPAC ファイルをインポートして新しい Azure SQL Database を作成する

**1 つのデータベース**

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

この記事は、PowerShell で BACPAC ファイルをインポートして Azure SQL Database を作成する手順について説明します。

BACPAC は、データベース スキーマとデータを含む .bacpac ファイルです。詳細については、「[データ層アプリケーション](https://msdn.microsoft.com/library/ee210546.aspx)」の「バックアップ パッケージ (.bacpac)」を参照してください。

Azure Storage BLOB コンテナーからインポートされた BACPAC からデータベースが作成されます。Azure Storage に .bacpac ファイルがない場合は、「[PowerShell を使用して BACPAC ファイルに Azure SQL Database をアーカイブする](sql-database-export-powershell.md)」の手順に従って作成できます。

> [AZURE.NOTE] Azure SQL Database では、復元できるすべてのユーザー データベースのバックアップが自動的に作成され、保守されます。詳細については、[SQL Database 自動バックアップ](sql-database-automated-backups.md)に関するページをご覧ください。


SQL Database をインポートするには、以下が必要です。

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合、このページの上部の**無料試用版**をクリックしてからこの記事に戻り、最後まで完了してください。
- インポートするデータベースの .bacpac ファイル (BACPAC)。BACPAC は、[Azure ストレージ アカウント (クラシック)](../storage/storage-create-storage-account.md) の BLOB コンテナー内にある必要があります。



[AZURE.INCLUDE [PowerShell セッションの開始](../../includes/sql-database-powershell.md)]



## 使用環境用の変数の設定

変数には、例の値を、使用するデータベースとストレージ アカウントの特定の値に置き換える必要があるものがいくつかあります。

サーバー名は、前の手順で選択したサブスクリプション内に現在存在しており、データベースを作成するサーバーを指定する必要があります。エラスティック プールへのデータベースの直接インポートはサポートされていませんが、最初に 1 つのデータベースにインポートしてからプールにデータベースを移動できます。

データベース名は、新しいデータベースの名前です。

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


BACPAC が配置されているストレージ アカウントの変数を以下に示します。[Azure ポータル](https://portal.azure.com)でストレージ アカウントを参照し、これらの値を取得します。プライマリ アクセス キーは、ストレージ アカウントのブレードで **[すべての設定]** をクリックしてから **[キー]** をクリックすることで検索できます。

BLOB 名は、データベースの作成元の、既存の .bacpac ファイルの名前です。.bacpac 拡張子を含める必要があります。

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


**Get-Credential** コマンドレットを実行すると、ユーザー名とパスワードの入力を求めるウィンドウが開きます。SQL Database サーバー (上記の $ServerName) の管理ログインとパスワードを入力します。Azure アカウントのユーザー名とパスワードではありません。

    $credential = Get-Credential


## データベースのインポート

このコマンドでは、サービスにデータベース のインポート要求を送信します。データベースのサイズに応じて、インポート操作の完了に時間がかかる場合があります。

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMxSize 50000
    

## 操作の進行状況の監視

**New-AzureRmSqlDatabaseImport** の実行後に、要求の状態を確認できます。

要求を送信した直後に状態を確認すると、通常は、**保留中**または**実行中**の状態が返され、現在の完了率が示されます。このため、出力に **Status : Completed** が表示されるまで何度も確認を実行できます。

このコマンドを実行すると、パスワードの入力を求められます。SQL Server の管理ログインとパスワードを入力します。


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink
 


## SQL Database の PowerShell インポート スクリプト


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey  StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMxSize 50000
 
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink

    

## 次のステップ

- [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)




## その他のリソース

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [災害復旧訓練](sql-database-disaster-recovery-drills.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0706_2016-->