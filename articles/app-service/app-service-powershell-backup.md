<properties
	pageTitle="PowerShell を使用して App Service アプリのバックアップと復元を実行する"
	description="PowerShell を使用して、Azure App Service でアプリのバックアップと復元を行う方法について説明します。"
	services="app-service"
	documentationCenter=""
	authors="NKing92"
	manager="wpickett"
    editor="" />

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="nicking"/>
# PowerShell を使用して App Service アプリのバックアップと復元を実行する

> [AZURE.SELECTOR]
- [PowerShell](app-service-powershell-backup.md)
- [REST API](../app-service-web/websites-csm-backup.md)

Azure PowerShell を使用して [App Service アプリ](https://azure.microsoft.com/services/app-service/web/)のバックアップと復元を実行する方法について説明します。Web アプリのバックアップと、要件や制限事項の詳細については、「[Azure App Service での Web アプリのバックアップ](../app-service-web/web-sites-backup.md)」を参照してください。

## 前提条件
PowerShell を使用してアプリのバックアップを行うには、以下が必要になります。

- Azure ストレージ コンテナーへの読み書きを可能にする **A SAS URL**。SAS URL の詳細については、「[SAS モデルについて](../storage/storage-dotnet-shared-access-signature-part-1.md)」を参照してください。PowerShell を使用して Azure Storage を管理する例については、「[Azure Storage での Azure PowerShell の使用](../storage/storage-powershell-guide-full.md)」を参照してください。
- Web アプリと共にデータベースをバックアップする場合は、**データベース接続文字列**。

### Web アプリのバックアップ コマンドレットで使用する SAS URL を生成する方法
PowerShell では、SAS URL を生成できます。この記事で説明するコマンドレットで使用できる SAS URL は、次のように生成します。

		$storageAccountName = "<your storage account's name>"
		$storageAccountRg = "<your storage account's resource group>"

		# This returns an array of keys for your storage account. Be sure to select the appropriate key. Here we select the first key as a default.
		$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccountRg -Name $storageAccountName
		$context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey[0].Value

		$blobContainerName = "<name of blob container for app backups>"
		$sasUrl = New-AzureStorageContainerSASToken -Name $blobContainerName -Permission rwdl -Context $context -ExpiryTime (Get-Date).AddMonths(1) -FullUri

## Azure PowerShell 1.3.2 以上をインストールする

Azure PowerShell のインストール手順と使用方法については、「[Azure Resource Manager での Azure PowerShell の使用](../powershell-install-configure.md)」を参照してください。

## バックアップの作成

Web アプリのバックアップを作成するには、New-AzureRmWebAppBackup コマンドレットを使用します。

		$sasUrl = "<your SAS URL>"
		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"

		$backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

これにより、自動生成された名前で、バックアップが作成されます。バックアップの名前を指定したい場合は、BackupName オプション パラメーターを使用します。

		$backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

データベースをバックアップに含めるには、最初に New-AzureRmWebAppDatabaseBackupSetting コマンドレットを使用してデータベース バックアップ設定を作成し、次にその設定を New-AzureRmWebAppBackup コマンドレットの Databases パラメーターに指定します。Databases パラメーターはデータベース設定の配列を受け取るため、複数のデータベースをバックアップできるようになります。

		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## バックアップの取得

Get-AzureRmWebAppBackupList コマンドレットは Web アプリのすべてのバックアップの配列を返します。Web アプリとそのリソース グループの名前を指定する必要があります。

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

特定のバックアップを取得するには、Get-AzureRmWebAppBackup コマンドレットを使用します。

		$backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

利便性のため、Web アプリのオブジェクトをいずれかのバックアップ管理コマンドレットにパイプすることもできます。

		$app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
		$backupList = $app | Get-AzureRmWebAppBackupList
		$backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## 自動バックアップのスケジュールを作成する

バックアップが指定された間隔で自動的に実行されるようにスケジュールすることができます。バックアップのスケジュールを構成するには、Edit-AzureRmWebAppBackupConfiguration コマンドレットを使用します。このコマンドレットは、いくつかのパラメーターを受け取ります。

- **Name** - Web アプリの名前。
- **ResourceGroupName** - Web アプリを含むリソース グループの名前。
- **Slot** - 省略可能。Web アプリ スロットの名前。
- **StorageAccountUrl** -バックアップの格納に使用する Azure Storage コンテナーの SAS URL。
- **FrequencyInterval** - バックアップの頻度を示す数値。正の整数にする必要があります。
- **FrequencyUnit** - バックアップの頻度を示す時間の単位。時間または日です。
- **RetentionPeriodInDays** - 自動バックアップが自動的に削除されるまでの保存日数。
- **StartTime** - 省略可能。自動バックアップを開始する時刻。これが null の場合、バックアップが直ちに開始されます。DateTime にする必要があります。
- **Databases** - 省略可能。バックアップするデータベースの DatabaseBackupSettings の配列。
- **KeepAtLeastOneBackup** - 省略可能なスイッチ パラメーター。保存期間に関係なく、常に 1 つのバックアップをストレージ アカウントに保持する必要がある場合に指定します。

このコマンドレットを使用する方法の例を次に示します。

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$slotName = "StagingSlot"
		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		Edit-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName -Slot $slotName `
		  -StorageAccountUrl "<your SAS URL>" -FrequencyInterval 6 -FrequencyUnit Hour -Databases $dbSetting1,$dbSetting2 `
		  -KeepAtLeastOneBackup -StartTime (Get-Date).AddHours(1)

最新のバックアップのスケジュールを取得するには、Get-AzureRmWebAppBackupConfiguration コマンドレットを使用します。これは、既に構成されているスケジュールを変更する場合に便利です。

		$configuration = Get-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName

		# Modify the configuration slightly
		$configuration.FrequencyInterval = 2
		$configuration.FrequencyUnit = "Day"

		# Apply the new configuration by piping it into the Edit-AzureRmWebAppBackupConfiguration cmdlet
		$configuration | Edit-AzureRmWebAppBackupConfiguration

## Web アプリをバックアップから復元する

Web アプリをバックアップから復元するには、Restore-AzureRmWebAppBackup コマンドレットを使用します。このコマンドレットを使用する場合、Get-AzureRmWebAppBackup cmdlet または Get-AzureRmWebAppBackupList コマンドレットから取得したバックアップ オブジェクトにパイプする方法が最も簡単です。

作成されたバックアップ オブジェクトは、Restore-AzureRmWebAppBackup コマンドレットにパイプすることができます。Web アプリの内容をバックアップの内容で上書きすることを示すには、Overwrite スイッチ パラメーターを指定します。バックアップにデータベースが含まれる場合は、これらのデータベースも復元されます。

		$backup | Restore-AzureRmWebAppBackup -Overwrite

Restore-AzureRmWebAppBackup ですべてのパラメーターを指定する方法の例を次に示します。

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$slotName = "StagingSlot"
		$blobName = "ContosoBackup.zip"
		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## バックアップの削除

バックアップを削除するには、AzureRmWebAppBackup コマンドレットを使用します。これにより、ストレージ アカウントからバックアップが削除されます。アプリ名、そのリソース グループ、削除するバックアップの ID を指定します。

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

バックアップ オブジェクトを Remove-AzureRmWebAppBackup コマンドレットにパイプして削除することもできます。

		$backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
		$backup | Remove-AzureRmWebAppBackup -Overwrite

<!---HONumber=AcomDC_0921_2016-->