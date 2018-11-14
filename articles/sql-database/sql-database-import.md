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
ms.date: 11/01/2018
ms.openlocfilehash: 913e374e51b968650b14d3f6563f64c217b76ad0
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913810"
---
# <a name="quickstart-import-a-bacpac-file-to-a-new-azure-sql-database"></a>クイック スタート: BACPAC ファイルを新しい Azure SQL Database にインポートする

データベースをアーカイブからインポートまたは別のプラットフォームから移行するときに、[BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルからデータベース スキーマとデータをインポートできます。 BACPAC ファイルは、メタデータと SQL Server データベースからのデータを含み、BACPAC の拡張子を持つ ZIP ファイルです。 BACPAC ファイルは Azure Blob Storage (Standard ストレージのみ) またオンプレミスの保存先のローカル ストレージからインポートできます。 インポート速度を最大限高めるために、より高いサービス レベルとコンピューティング サイズ (P6 など) を指定し、インポートが正常に完了した後は必要に応じてスケールダウンすることをお勧めします。 また、インポート後のデータベースの互換性レベルはソース データベースの互換性レベルに基づきます。

> [!IMPORTANT]
> データベースを Azure SQL Database に移行した後に、データベースを現在の互換性レベル (AdventureWorks2008R2 データベースの場合はレベル 100) またはそれ以上のレベルで運用できます。 データベースを指定の互換性レベルで運用する影響とオプションについて詳しくは、[ALTER DATABASE の互換性レベル](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)に関する記事をご覧ください。 また、互換性レベルに関連する追加のデータベースレベルの設定について詳しくは、[ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) に関する記事をご覧ください。

## <a name="import-from-a-bacpac-file-using-azure-portal"></a>Azure Portal を使用して BACPAC ファイルからインポートする

この記事では、[Azure Portal](https://portal.azure.com) を使って、Azure Blob Storage に保存されている BACPAC ファイルから Azure SQL Database を作成する手順について説明します。 Azure Portal を使用する場合は、Azure Blob Storage からのインポートのみをサポートします。

Azure portal を使用してデータベースをインポートするには、データベースに関連付けられたサーバーのページ (データベースのページではありません) を開き、ツールバーの **[インポート]** をクリックします。 ストレージ アカウントとコンテナーを指定し、インポートする BACPAC ファイルを選択します。 新しいデータベースのサイズ (通常、元のサイズと同じ) を選択し、インポート先の SQL Server 資格情報を指定します。  

   ![データベースのインポート](./media/sql-database-import/import.png)

インポート操作の進行状況を監視するには、インポートされるデータベースが含まれる論理サーバーのページを開きます。 **[操作]** までスクロールして、**[インポート/エクスポート]** 履歴をクリックします。

> [!NOTE]
> [Azure SQL Database Managed Instance](sql-database-managed-instance.md) では、この記事の他の方法で BACPAC ファイルをインポートできますが、現在のところ、Azure Portal を利用した移行には対応していません。

### <a name="monitor-the-progress-of-an-import-operation"></a>インポート操作の進行状況の監視

インポート操作の進行状況を監視するには、データベースがインポートされる論理サーバーのページを開きます。 **[操作]** までスクロールして、**[インポート/エクスポート]** 履歴をクリックします。

   ![インポート](./media/sql-database-import/import-history.png) ![インポートの状態](./media/sql-database-import/import-status.png)

データベースがサーバーで稼働していることを確認するには、**[SQL データベース]** をクリックし、新しいデータベースが **[オンライン]** であることを確認します。

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>SQLPackage を使用して BACPAC ファイルからインポートする

[SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) コマンドライン ユーティリティを使用して SQL データベースをインポートするには、「[Import のパラメーターとプロパティ](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)」をご覧ください。 SQLPackage ユーティリティは、最新バージョンの [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) および [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) に付属しています。また、Microsoft ダウンロード センターから最新バージョンの [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) を直接ダウンロードすることもできます。

ほとんどの運用環境では、スケールとパフォーマンスのために、SQLPackage ユーティリティの使用をお勧めします。 BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。

**AdventureWorks2008R2** データベースをローカル ストレージから Azure SQL Database 論理サーバーにインポートするスクリプト例については、次の SQLPackage コマンドをご覧ください。この例では **mynewserver20170403** という名前です。 このスクリプトは、サービス階層が **Premium**、サービス目標が **P6** の **myMigratedDatabase** という新しいデータベースを作成する方法を示します。 これらの値は、お使いの環境に合わせて変更してください。

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=ServerAdmin;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Azure SQL Database 論理サーバーは、ポート 1433 でリッスンします。 企業のファイアウォール内から Azure SQL Database 論理サーバーに接続する場合、企業のファイアウォールでこのポートが開放されていないと、正しく接続することができません。
>

この例では、SqlPackage.exe と Active Directory ユニバーサル認証を使用してデータベースをインポートする方法を示します。

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>PowerShell を使用して BACPAC ファイルからインポートする

[New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) コマンドレットを使用して、Azure SQL Database サービスにデータベースのインポート要求を送信します。 データベースのサイズに応じて、インポート操作の完了に時間がかかる場合があります。

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName $storageaccountname).Value[0] `
    -StorageUri "http://$storageaccountname.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "ServerAdmin" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "ASecureP@assw0rd" -AsPlainText -Force)

 ```

インポート要求の状態を確認するには、[Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) コマンドレットを使用します。 要求直後にこれを実行すると、通常は、 **Status : InProgress**が返されます。 **Status : Succeeded** が表示された場合、インポートは完了しています。

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

- エラスティック プール内のデータベースへのインポートはサポートされていません。 データを単一データベースにインポートしてから、そのデータベースをプールに移動することはできます。

## <a name="import-using-other-methods"></a>他の方法によるインポート

次のウィザードも利用できます。

- [SQL Server Management Studio のデータ層アプリケーションのインポート ウィザード](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard)。
- [SQL Server のインポートおよびエクスポート ウィザード](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)。

## <a name="next-steps"></a>次の手順

- インポートされた SQL データベースへの接続とクエリの実行については、「[SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)」を参照してください。
- BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。
- パフォーマンスの推奨事項も含む、SQL Server データベースの移行プロセス全体の詳細については、[Azure SQL Database への SQL Server データベースの移行](sql-database-cloud-migrate.md)に関するページを参照してください。
- ストレージ キーと共有アクセス署名を管理および共有する方法については、「[Azure Storage セキュリティ ガイド](https://docs.microsoft.com/azure/storage/common/storage-security-guide)」をご覧ください。
