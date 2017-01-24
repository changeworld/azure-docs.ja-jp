---
title: "PowerShell で BACPAC ファイルをインポートして Azure SQL Database を作成する | Microsoft Docs"
description: "PowerShell で BACPAC ファイルをインポートして Azure SQL データベースを作成する"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 8d78da13-43fe-4447-92e0-0a41d0321fd4
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 08/31/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 75bf523679c8d8ad6fbe4a8aa8a561d03008e59b
ms.openlocfilehash: 211f416d05b0ca998cd71a78d091b8efa39f6a7b


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>PowerShell で BACPAC ファイルをインポートして Azure SQL データベースを作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> 
> 

この記事では、PowerShell で [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルをインポートして Azure SQL Database を作成する手順について説明します。

Azure Storage BLOB コンテナーからインポートされた BACPAC (.bacpac) ファイルからデータベースが作成されます。 Azure Storage に BACPAC ファイルがない場合は、「 [PowerShell を使用して BACPAC ファイルに Azure SQL Database をアーカイブする](sql-database-export-powershell.md)」を参照してください。 Azure Storage にない BACPAC ファイルを持っている場合は、 [AzCopy を使用すると、簡単にそのファイルを Azure Storage アカウントにアップロード](../storage/storage-use-azcopy.md#blob-upload)できます。

> [!NOTE]
> Azure SQL Database では、復元できるすべてのユーザー データベースのバックアップが自動的に作成され、保守されます。 詳細については、「 [SQL Database 自動バックアップ](sql-database-automated-backups.md)」を参照してください。
> 
> 

SQL Database をインポートするには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合、このページの上部の **無料試用版** をクリックしてからこの記事に戻り、最後まで完了してください。
* インポートするデータベースの BACPAC ファイル。 BACPAC は、 [Azure Storage アカウント](../storage/storage-create-storage-account.md) の BLOB コンテナー内にある必要があります。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="set-up-the-variables-for-your-environment"></a>使用環境用の変数の設定
変数には、例の値を、使用するデータベースとストレージ アカウントの特定の値に置き換える必要があるものがいくつかあります。

サーバー名は、前の手順で選択したサブスクリプションに現在存在するサーバーにする必要があります。 このサーバーが、データベースの作成先サーバーになります。 エラスティック プールにデータベースを直接インポートすることはできません。 ただし、最初にデータベースを単一データベースとしてインポートしてから、プールに移動することができます。

データベース名は、新しいデータベースの名前です。

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


BACPAC が配置されているストレージ アカウントの変数を以下に示します。 [Azure Portal](https://portal.azure.com)でストレージ アカウントを参照し、これらの値を取得します。 プライマリ アクセス キーは、ストレージ アカウントのブレードで **[すべての設定]** をクリックしてから **[キー]** をクリックすることで検索できます。

BLOB 名は、データベースの作成元の、既存の BACPAC ファイルの名前です。 .bacpac 拡張子を含める必要があります。

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


[Get-Credential](https://msdn.microsoft.com/library/azure/hh849815\(v=azure.300\).aspx) コマンドレットを実行すると、ユーザー名とパスワードの入力を求めるウィンドウが開きます。 SQL Database サーバー (上記の $ServerName) の管理ログインとパスワードを入力します。Azure アカウントのユーザー名とパスワードではありません。

    $credential = Get-Credential


## <a name="import-the-database"></a>データベースのインポート
このコマンドでは、サービスにデータベース のインポート要求を送信します。 データベースのサイズに応じて、インポート操作の完了に時間がかかる場合があります。

    $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -StorageKeytype $StorageKeyType -StorageKey $StorageKey -StorageUri $StorageUri -AdministratorLogin $credential.UserName -AdministratorLoginPassword $credential.Password -Edition Standard -ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## <a name="monitor-the-progress-of-the-operation"></a>操作の進行状況の監視
[New-AzureRmSqlDatabaseImport](https://msdn.microsoft.com/library/azure/mt707793\(v=azure.300\).aspx) の実行後に、[Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx) を実行して要求の状態を確認できます。

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="sql-database-powershell-import-script"></a>SQL Database の PowerShell インポート スクリプト
    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -StorageKeytype $StorageKeyType -StorageKey $StorageKey -StorageUri $StorageUri -AdministratorLogin $credential.UserName -AdministratorLoginPassword $credential.Password -Edition Standard -ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="next-steps"></a>次のステップ
* インポートされた SQL Database への接続とクエリの実行については、「 [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)




<!--HONumber=Dec16_HO3-->


