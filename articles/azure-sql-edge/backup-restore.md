---
title: データベースのバックアップと復元 - Azure SQL Edge (プレビュー)
description: Azure SQL Edge (プレビュー) のバックアップと復元の機能について説明します
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 902576f82faa18fbb0e7c7eaed5c06993bd379cc
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235180"
---
# <a name="backup-and-restore-databases-in-azure-sql-edge-preview"></a>Azure SQL Edge (プレビュー) でのデータベースのバックアップと復元 

Azure SQL Edge は Linux 上の Microsoft SQL Server データベース エンジンの最新バージョンに基づいて構築されており、SQL Server on Linux や コンテナーで実行されている SQL Server で利用できるのと同様のデータベースのバックアップおよび復元機能が提供されます。 バックアップと復元のコンポーネントにより、Azure SQL Edge データベースに格納されているデータを保護するうえで不可欠な保護機能が提供されます。 致命的なデータ損失のリスクを最小限に抑えるには、データベースを定期的にバックアップして、データの変更を定期的に保持することをお勧めします。 十分に計画されたバックアップおよび復元戦略は、さまざまな障害が原因で発生するデータ損失からデータベースを保護します。 一連のバックアップの復元とデータベースの回復を実行することでご自分の戦略をテストして、災害に効率的に対応するための準備を整えてください。

バックアップが重要である理由の詳細については、「[SQL Server データベースのバックアップと復元](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/)」をご覧ください。

Azure SQL Edge では、ローカル ストレージまたは Azure BLOB のどちらを使用したバックアップおよび復元もサポートされています。 Azure Blob Storage を使用したバックアップと復元の詳細については、「[Microsoft Azure Blob Storage サービスを使用した SQL Server のバックアップと復元](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/)」と「[SQL Server Backup to URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)」を参照してください。

## <a name="backing-up-a-database-in-azure-sql-edge"></a>Azure SQL Edge でのデータベースのバックアップ

Azure SQL Edge では、SQL Server でサポートされているのと同じバックアップの種類がサポートされています。 SQL Server でサポートされているバックアップの種類の完全な一覧については、[バックアップの概要](/sql/relational-databases/backup-restore/backup-overview-sql-server/)に関する記事を参照してください。

> [!IMPORTANT] 
> Azure SQL Edge で作成されたデータベースでは、既定で単純復旧モデルが使用されます。 そのため、これらのデータベースではログ バックアップを実行できません。 これらのデータベースでログ バックアップを実行する必要がある場合は、管理者がデータベースの復旧モデルを完全復旧モデルに変更する必要があります。 SQL Server でサポートされている復旧モデルの完全な一覧については、「[復旧モデルの概要](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov)」を参照してください。

### <a name="backup-to-local-disk"></a>ローカル ディスクへのバックアップ

以下に示す例では、Transact-SQL コマンド BACKUP DATABASE を使って、コンテナー内にデータベースのバックアップを作成します。 この例の目的を示すために、バックアップ ファイルを格納するための "backup" という名前の新しいフォルダーを作成します。

1. バックアップ用のフォルダーを作成します。 このコマンドは、Azure SQL Edge コンテナーが実行されているホスト上で実行する必要があります。 次のコマンドで、 **<AzureSQLEdge_Container_Name>** をご自分のデプロイの Azure SQL Edge コンテナーの名前に置き換えてください。

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. SQL Server Management Studio (SSMS) または Azure Data Studio (ADS) を使用して Azure SQL Edge インスタンスに接続し、BACKUP DATABASE コマンドを実行してユーザー データベースのバックアップを作成します。 次の例では、*IronOreSilicaPrediction* データベースのバックアップを作成します。

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. このコマンドを実行し、データベースのバックアップに成功した場合は、SSMS または ADS の結果セクションに次のようなメッセージが表示されます。

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="backup-to-url"></a>Backup to URL

Azure SQL Edge では、ページ BLOB とブロック BLOB の両方へのバックアップがサポートされています。 ページ BLOB とブロック BLOB の詳細については、「[ブロック BLOB とページ BLOB へのバックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-ver15#blockbloborpageblob)」を参照してください。 次の例では、データベース *IronOreSilicaPrediction* がブロック BLOB にバックアップされます。 

1. ブロック BLOB へのバックアップを構成するための最初の手順は、Azure SQL Edge で SQL Server 資格情報を作成するために使用できる Shared Access Signature (SAS) トークンを生成することです。 このスクリプトでは、保存されているアクセス ポリシーに関連付けられた Shared Access Signature が作成されます。 詳細については、「[Shared Access Signature、第 1 部:SAS モデル](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)に関するページを参照してください。 スクリプトはまた、SQL Server に対する資格情報を作成するのに必要な T-SQL コマンドも書き込みます。 次のスクリプトは、ストレージ アカウントを持つ Azure サブスクリプションとバックアップ用のストレージ コンテナーを既にお持ちであることを前提としています。

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    スクリプトが正常に実行されたら、CREATE CREDENTIAL コマンドをクエリ ツールにコピーし、SQL Server のインスタンスに接続し、コマンドを実行して Shared Access Signature を持つ資格情報を作成します。

2. SQL Server Management Studio (SSMS) または Azure Data Studio (ADS) を使用して Azure SQL Edge インスタンスに接続し、前の手順のコマンドを使用して資格情報を作成します。 CREATE CREDENTIAL コマンドは、前の手順の実際の出力で置き換えてください。

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. 次のコマンドを実行すると、*IronOreSilicaPrediction* のバックアップが Azure ストレージ コンテナーに作成されます。

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restoring-a-database-in-azure-sql-edge"></a>Azure SQL Edge でのデータベースの復元

Azure SQL Edge では、ローカル ディスク、ネットワークの場所から、または Azure Blob Storage アカウントからの復元がサポートされています。 SQL Server での復元と復旧の概要については、[復元と復旧の概要](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-ver15)に関する記事を参照してください。 SQL Server での単純復旧モデルの概要については、「[データベースの全体復元 (単純復旧モデル)](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model?view=sql-server-ver15)」を参照してください。

### <a name="restore-from-local-disk"></a>ローカル ディスクから復元する

この例では、前の例で実行した *IronOreSilicaPrediction* のバックアップを使用して、別の名前を持つ新しいデータベースとして復元を行います。

1. データベースのバックアップ ファイルがコンテナー内にまだ存在していない場合は、次のコマンドを使用してコンテナー内にファイルをコピーできます。 次の例は、バックアップ ファイルがローカル ホスト上に存在し、sql1 という名前の Azure SQL Edge コンテナー内の /var/opt/mssql/backup フォルダーにコピーされることを前提としています。

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. SQL Server Management Studio (SSMS) または Azure Data Studio (ADS) を使用して Azure SQL Edge インスタンスに接続し、復元コマンドを実行します。 次の例では、**IronOrePredictDB.bak** が復元され、新しいデータベース **IronOreSilicaPrediction_2** が作成されます

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. 復元コマンドを実行し、復元操作が正常に完了すると、出力ウィンドウに次のようなメッセージが表示されます。 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>URL から復元

Azure SQL Edge では、Azure Storage アカウントからのデータベースの復元もサポートされています。 復元は、ブロック BLOB またはページ BLOB のいずれかのバックアップから実行できます。 次の例では、ブロック BLOB のデータベース バックアップ ファイル *IronOreSilicaPrediction_2020_04_16.bak* を復元して、データベース *IronOreSilicaPrediction_3* が作成されます。

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


