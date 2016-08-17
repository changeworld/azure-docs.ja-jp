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

この記事では、PowerShell で [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルをインポートして Azure SQL Database を作成する手順について説明します。

Azure Storage BLOB コンテナーからインポートされた BACPAC ファイルからデータベースが作成されます。Azure Storage に .bacpac ファイルがない場合は、「[PowerShell を使用して BACPAC ファイルに Azure SQL Database をアーカイブする](sql-database-export-powershell.md)」の手順に従って作成できます。

> [AZURE.NOTE] Azure SQL Database では、復元できるすべてのユーザー データベースのバックアップが自動的に作成され、保守されます。詳細については、「[SQL Database 自動バックアップ](sql-database-automated-backups.md)」をご覧ください。


SQL Database をインポートするには、以下が必要です。

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合は、このページの上部の **[無料試用版]** をクリックしてからこの記事に戻り、最後まで完了してください。
- インポートするデータベースの .bacpac ファイル。BACPAC は、[Azure ストレージ アカウント (クラシック)](../storage/storage-create-storage-account.md) の BLOB コンテナー内にある必要があります。



[AZURE.INCLUDE [PowerShell セッションの開始](../../includes/sql-database-powershell.md)]



## 使用環境用の変数の設定

変数には、例の値を、使用するデータベースとストレージ アカウントの特定の値に置き換える必要があるものがいくつかあります。

サーバー名は、前の手順で選択したサブスクリプションに現在存在するサーバーにする必要があります。このサーバーが、データベースの作成先サーバーになります。エラスティック プールへのデータベースの直接インポートはサポートされていないことに注意してください。ただし、最初にデータベースをシングル データベースにインポートしてから、プールに移動することができます。

データベース名は、新しいデータベースの名前です。

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


BACPAC が配置されているストレージ アカウントの変数を以下に示します。[Azure ポータル](https://portal.azure.com)でストレージ アカウントを参照し、これらの値を取得します。プライマリ アクセス キーは、ストレージ アカウントのブレードで **[すべての設定]** をクリックしてから **[キー]** をクリックすることで検索できます。

BLOB 名は、データベースの作成元の、既存の .bacpac ファイルの名前です。.bacpac 拡張子を含める必要があります。

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


**Get-Credential** コマンドレットを実行すると、ユーザー名とパスワードの入力を求めるウィンドウが開きます。SQL Database サーバー (上記の $ServerName) の管理ログインとパスワードを入力します。Azure アカウントのユーザー名とパスワードではありません。

    $credential = Get-Credential


## データベースのインポート

このコマンドでは、サービスにデータベース のインポート要求を送信します。データベースのサイズに応じて、インポート操作の完了に時間がかかる場合があります。

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## 操作の進行状況の監視

**New-AzureRmSqlDatabaseImport** の実行後に、要求の状態を確認できます。

要求直後に次を実行すると、通常は、"**Status : Pending**" または "**Status : Running**" が返されます。"**Status : Completed**" が出力されるまで、これを複数回実行します。

このコマンドを実行すると、パスワードの入力を求められます。SQL Server の管理パスワードを入力します。


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink



## SQL Database の PowerShell インポート スクリプト


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink



## 次のステップ

- インポートされた SQL Database への接続とクエリの実行については、「[SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)」を参照してください。

<!---HONumber=AcomDC_0803_2016-->