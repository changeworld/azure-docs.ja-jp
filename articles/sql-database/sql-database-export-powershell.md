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
	ms.date="07/19/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# PowerShell を使用して BACPAC ファイルに Azure SQL Database をアーカイブする

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


この記事では PowerShell を使用して Azure Blob Storage に格納されている BACPAC ファイルに Azure SQL Database をアーカイブする方法について説明します。

Azure SQL Database のアーカイブを作成する必要がある場合は、BACPAC ファイルにデータベース スキーマとデータをエクスポートできます。BACPAC ファイルは、単に BACPAC の拡張子を持つ ZIP ファイルです。BACPAC ファイルは、後で Azure Blob Storage またはオンプレミスの場所にあるローカル ストレージに格納することや、後で Azure SQL Database またはオンプレミス SQL Server インストールに戻ってインポートすることができます。

***考慮事項***

- アーカイブにトランザクション一貫性を持たせるために、書き込みアクティビティがエクスポート中に発生していないことを確認するか、または Azure SQL Database の[トランザクション一貫性のあるコピー](sql-database-copy.md)からエクスポートする必要があります
- Azure Blob Storage にアーカイブされた BACPAC ファイルの最大サイズは 200 GB です。[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) コマンド プロンプト ユーティリティを使用して、より大きな BACPAC ファイルをローカル ストレージにアーカイブします。このユーティリティは、Visual Studio と SQL Server の両方に含まれます。SQL Server Data Tools の最新版を[ダウンロード](https://msdn.microsoft.com/library/mt204009.aspx)し、このユーティリティを入手することもできます。
- BACPAC ファイルを使用する Azure Premium Storage へのアーカイブはサポートされていません。
- エクスポート操作が 20 時間以上を超える場合は取り消されることがあります。エクスポート中にパフォーマンスを向上させるには、次の操作を実行します。
 - サービス レベルを一時的に上げる
 - エクスポート中のすべての読み取りと書き込みアクティビティを中止する
 - すべての大きなテーブルにクラスター化インデックスを使用する。クラスター化インデックスがないと、エクスポートが 6 ～ 12 時間よりも時間が長くかかる場合には失敗することがあります。これは、エクスポート サービスがテーブル スキャンを実行してテーブル全体をエクスポートしようとする必要があることが原因です。
 
> [AZURE.NOTE] BACPAC はバックアップおよび復元操作に使用するためのものでありません。Azure SQL Database では、すべてのユーザー データベースのバックアップが自動的に作成されます。詳細については、[SQL Database 自動バックアップ](sql-database-automated-backups.md)に関するページをご覧ください。

この記事を完了するには、以下が必要です。

- Azure サブスクリプション。
- Azure SQL Database。
- [Azure Standard Storage アカウント](../storage/storage-create-storage-account.md) (標準的なストレージに BACPAC を格納する BLOB コンテナーを含む)。


[AZURE.INCLUDE [PowerShell セッションの開始](../../includes/sql-database-powershell.md)]


## 特定の環境用の変数の設定

変数には、例の値を、使用するデータベースとストレージ アカウントの特定の値に置き換える必要があるものがいくつかあります。

特定の値に置き換えます。

    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"


[Azure ポータル](https://portal.azure.com)でストレージ アカウントを参照し、これらの値を取得します。プライマリ アクセス キーは、ストレージ アカウントのブレードで **[すべての設定]** をクリックしてから **[キー]** をクリックすることで検索できます。

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


**Get-Credential** コマンドレットを実行すると、ユーザー名とパスワードの入力を求めるウィンドウが開きます。SQL Server の管理ログインとパスワード (Azure アカウントのユーザー名とパスワードではない) を入力します。

    $credential = Get-Credential

## データベースのエクスポート

このコマンドでは、サービスにデータベースのエクスポート要求を送信します。データベースのサイズに応じて、エクスポート操作の完了に時間がかかる場合があります。

> [AZURE.IMPORTANT] トランザクションに関する BACPAC ファイルの一貫性を保証するには、最初に[データベースのコピーを作成](sql-database-copy-powershell.md)してエクスポートする必要があります。


    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password
    

## エクスポート操作の進行状況の監視

**New-AzureRmSqlDatabaseExport** の実行後に、要求の状態を確認できます。要求直後にこれを実行すると、通常は、**Status : Pending** または **Status : Running** が返されます。したがって、出力に **Status : Completed** が表示されるまで、これを複数回実行できます。

このコマンドを実行すると、パスワードの入力を求められます。SQL Server の管理パスワードを入力します。


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink
    


## SQL Database のエクスポートの PowerShell スクリプト


    $ServerName = "servername"
    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey  StorageUri $StorageUri –AdministratorLogin $credential.UserName  –AdministratorPassword $credential.Password

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink

## 次のステップ

- [Azure SQL Database のインポート](sql-database-import-powershell.md)


## その他のリソース

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [災害復旧訓練](sql-database-disaster-recovery-drills.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0720_2016-->