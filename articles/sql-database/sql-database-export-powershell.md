<properties
    pageTitle="PowerShell を使用して BACPAC ファイルに Azure SQL Database をアーカイブする"
    description="PowerShell を使用して BACPAC ファイルに Azure SQL Database をアーカイブする"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="08/01/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# PowerShell を使用して BACPAC ファイルに Azure SQL Database をアーカイブする

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


この記事では、Azure Blob Storage に格納されている [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルに Azure SQL Database をアーカイブする方法について説明します。この記事では、PowerShell を使用してこの操作を行う方法を示しています。

Azure SQL Database のアーカイブを作成する必要がある場合は、BACPAC ファイルにデータベース スキーマとデータをエクスポートできます。BACPAC ファイルは、単に .bacpac の拡張子を持つ ZIP ファイルです。BACPAC ファイルは、後で Azure Blob Storage やオンプレミスのローカル ストレージに格納できます。また、Azure SQL Database や オンプレミスの SQL Server インストールにインポートすることもできます。

**考慮事項**

- アーカイブにトランザクション一貫性を持たせるために、書き込みアクティビティがエクスポート中に行われないようにするか、または Azure SQL Database の[トランザクション一貫性のあるコピー](sql-database-copy.md)からエクスポートする必要があります。
- Azure Blob Storage にアーカイブできる BACPAC ファイルの最大サイズは 200 GB です。より大きな BACPAC ファイルをローカル ストレージにアーカイブするには、[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) コマンドプロンプト ユーティリティを使用します。このユーティリティは、Visual Studio と SQL Server の両方に含まれます。SQL Server Data Tools の最新版を[ダウンロード](https://msdn.microsoft.com/library/mt204009.aspx)し、このユーティリティを入手することもできます。
- BACPAC ファイルを使用した Azure Premium Storage へのアーカイブはサポートされていません。
- エクスポート操作が 20 時間を超える場合は取り消されることがあります。エクスポート中にパフォーマンスを向上させるには、次の操作を実行します。
 - サービス レベルを一時的に上げる。
 - エクスポート中のすべての読み取りアクティビティと書き込みアクティビティを中止する。
 - すべての大きなテーブルにクラスター化インデックスを使用する。クラスター化インデックスがないと、エクスポートが 6 ～ 12 時間よりも時間が長くかかる場合には失敗することがあります。これは、エクスポート サービスがテーブル スキャンを実行してテーブル全体をエクスポートしようとする必要があることが原因です。

> [AZURE.NOTE] BACPAC はバックアップおよび復元操作に使用するためのものでありません。Azure SQL Database では、すべてのユーザー データベースのバックアップが自動的に作成されます。詳細については、「[SQL Database 自動バックアップ](sql-database-automated-backups.md)」をご覧ください。

この記事を完了するには、以下が必要です。

- Azure サブスクリプション。
- Azure SQL Database。
- [Azure Standard Storage アカウント](../storage/storage-create-storage-account.md) (Standard Storage に BACPAC を格納する BLOB コンテナーを含む)。


[AZURE.INCLUDE [PowerShell セッションの開始](../../includes/sql-database-powershell.md)]




## データベースのエクスポート

[New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/mt707796.aspx) コマンドレットは、サービスに対してデータベースのエクスポート要求を送信します。データベースのサイズに応じて、エクスポート操作の完了に時間がかかる場合があります。

> [AZURE.IMPORTANT] BACPAC ファイルのトランザクション一貫性を確保するには、最初に[データベースのコピーを作成](sql-database-copy-powershell.md)してから、そのコピーをエクスポートする必要があります。


     $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password


## エクスポート操作の進行状況の監視

**New-AzureRmSqlDatabaseExport** の実行後に、[Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/mt707794.aspx) を実行して要求の状態を確認できます。要求直後にこれを実行すると、通常は、"**Status : InProgress**" が返されます。"**Status : Succeeded**" が表示された場合、エクスポートは完了しています。


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## SQL Database のエクスポート例

次の例では、既存の SQL Database を BACPAC にエクスポートしてから、エクスポート操作の状態を確認する方法を示します。

この例を実行する場合、いくつかの変数を、使用するデータベースとストレージ アカウントの固有の値に置き換える必要があります。ストレージ アカウント名、BLOB コンテナー名、およびキーの値を取得するには、[Azure ポータル](https://portal.azure.com)でストレージ アカウントを参照します。キーは、[ストレージ アカウント] ブレードにある **[アクセス キー]**をクリックして検索できます。

以下の `VARIABLE-VALUES` を Azure リソース固有の値に置換します。データベース名は、エクスポートする既存のデータベースになります。



    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## 次のステップ

- Powershell を使用して Azure SQL Database をインポートする方法については、[PowerShell を使用した BACPAC のインポート](sql-database-import-powershell.md)に関するページをご覧ください。


## その他のリソース

- [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/mt707796.aspx)
- [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/mt707794.aspx)

<!---HONumber=AcomDC_0803_2016-->