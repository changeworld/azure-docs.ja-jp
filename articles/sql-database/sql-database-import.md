---
title: BACPAC ファイルをインポートして Azure SQL Database を作成する | Microsoft Docs
description: BACPAC ファイルをインポートして新しい Azure SQL Database を作成します。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/05/2018
ms.openlocfilehash: 6753666f1747c95ad3486444ed41e3cad0b8e905
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084178"
---
# <a name="quickstart-import-a-bacpac-file-to-a-new-azure-sql-database"></a>クイック スタート:BACPAC ファイルを新しい Azure SQL Database にインポートする

[BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイル (データベースのメタデータとデータを含む `.bacpac` 拡張機能がある zip ファイル) を使用して、SQL Server データベースを Azure SQL データベースに移行することができます。 BACPAC ファイルは、Azure Blob Storage (Standard ストレージのみ) またオンプレミスの保存先のローカル ストレージからインポートできます。 インポート速度を最大限高めるために、より高いサービス レベルとコンピューティング サイズ (P6 など) を指定し、インポートが正常に完了した後はスケールダウンすることをお勧めします。 インポートされたデータベースの互換性レベルは、ソース データベースの互換性レベルに基づきます。

> [!IMPORTANT]
> データベースをデータベースに移行した後に、データベースを現在の互換性レベル (AdventureWorks2008R2 データベースの場合はレベル 100) またはそれ以上のレベルで運用できます。 データベースを指定の互換性レベルで運用する影響とオプションについて詳しくは、[ALTER DATABASE の互換性レベル](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)に関する記事をご覧ください。 また、互換性レベルに関連する追加のデータベースレベルの設定について詳しくは、[ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) に関する記事をご覧ください。

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Azure portal で BACPAC ファイルからインポートする

この記事では、[Azure portal](https://portal.azure.com) で、Azure Blob Storage に保存されている BACPAC ファイルから Azure SQL Database を作成する方法を示します。 このポータルは Azure Blob Storage からのインポート*のみ*をサポートします。

> [!NOTE]
> [Azure SQL Database Managed Instance](sql-database-managed-instance.md) では、この記事の他の方法で BACPAC ファイルをインポートできますが、現在のところ、Azure portal を利用した移行には対応していません。

Azure portal でデータベースをインポートするには、インポートをホストする論理サーバーのページを開き、ツールバーで **[データベースのインポート]** を選択します。  

   ![データベースのインポート](./media/sql-database-import/import.png)

ストレージ アカウントとコンテナーを選択し、インポートする BACPAC ファイルを選択します。 新しいデータベースのサイズ (通常、元のサイズと同じ) を指定し、インポート先の SQL Server 資格情報を指定します。 

### <a name="monitor-imports-progress"></a>インポートの進行状況を監視する

インポートの進行状況を監視するには、インポートされたデータベースの論理サーバーのページを開き、**[設定]** までスクロールして、**[インポート/エクスポート履歴]** を選択します。 成功すると、インポートは**完了**状態になります。

データベースがサーバーで稼働していることを確認するには、**[SQL データベース]** を選択し、新しいデータベースが **[オンライン]** であることを確認します。

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>SqlPackage を使用して BACPAC ファイルからインポートする

[SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) コマンドライン ユーティリティを使用して SQL データベースをインポートするには、「[Import のパラメーターとプロパティ](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)」をご覧ください。 SqlPackage には、最新バージョンの [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) と [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) が付属しています。 最新の [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) は、Microsoft ダウンロード センターからダウンロードすることもできます。

スケールとパフォーマンスを考慮して、ほとんどの運用環境では SqlPackage の使用をお勧めします。 BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。

次の SqlPackage コマンドは、**AdventureWorks2008R2** データベースを、ローカル ストレージから **mynewserver20170403** という Azure SQL Database 論理サーバーにインポートします。 **Premium** サービス層と **P6** サービス オブジェクトがある **myMigratedDatabase** という新しいデータベースが作成されます。 これらの値は、お使いの環境に合わせて変更してください。

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Azure SQL Database 論理サーバーは、ポート 1433 でリッスンします。 企業のファイアウォールの背後から論理サーバーに接続するには、そのファイアウォールでこのポートが開かれている必要があります。
>

この例では、SqlPackage と Active Directory ユニバーサル認証を使用してデータベースをインポートする方法を示します。

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>PowerShell を使用して BACPAC ファイルからインポートする

[New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) コマンドレットを使用して、Azure SQL Database サービスにデータベースのインポート要求を送信します。 データベースのサイズによって、インポートが完了するまでに時間がかかる場合があります。

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport 
    -ResourceGroupName "myResourceGroup" `
    -ServerName "myLogicalServer" `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName "myStorageAccount").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 インポートの進行状況を確認するには、[Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) コマンドレットを使用できます。 要求直後にこのコマンドレットを実行すると、通常は、 **Status :InProgress** が返されます。 **Status :Succeeded** が表示された場合、インポートは完了しています。

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
別のスクリプト例については、「[データベースを BACPAC ファイルからインポートする](scripts/sql-database-import-from-bacpac-powershell.md)」を参照してください。

## <a name="limitations"></a>制限事項

エラスティック プール内のデータベースへのインポートはサポートされていません。 データを単一データベースにインポートしてから、そのデータベースをプールに移動することはできます。

## <a name="import-using-wizards"></a>ウィザードを使用したインポート

次のウィザードも利用できます。

- [SQL Server Management Studio のデータ層アプリケーションのインポート ウィザード](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard)。
- [SQL Server のインポートおよびエクスポート ウィザード](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)。

## <a name="next-steps"></a>次の手順

- インポートした SQL Database に接続してそれを照会する方法については、「[クイック スタート:Azure SQL Database:SQL Server Management Studio を使って接続とデータの照会を行う](sql-database-connect-query-ssms.md)」という記事をご覧ください。
- BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。
- パフォーマンスの推奨事項も含む、SQL Server データベースの移行プロセス全体の詳細については、[Azure SQL Database への SQL Server データベースの移行](sql-database-cloud-migrate.md)に関するページを参照してください。
- ストレージ キーと共有アクセス署名を管理および共有する方法については、「[Azure Storage セキュリティ ガイド](https://docs.microsoft.com/azure/storage/common/storage-security-guide)」をご覧ください。
