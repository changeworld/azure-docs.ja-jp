<properties
	pageTitle="SQL Server Virtual Machines の自動バックアップ | Microsoft Azure"
	description="Azure Virtual Machines でリソース マネージャーのデプロイメント モデルを使用して実行されている SQL Server の自動バックアップ機能について説明します。"
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-resource-manager" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="11/12/2015"
	ms.author="jroth" />

# Azure Virtual Machines での SQL Server の自動バックアップ

自動バックアップでは、SQL Server 2014 Standard または Enterprise を実行する Azure VM 上の既存のデータベースと新しいデータベースのすべてを対象に、[Microsoft Azure へのマネージ バックアップ](https://msdn.microsoft.com/library/dn449496.aspx)が自動的に構成されます。これにより、永続的な Azure BLOB ストレージを利用した日常的なデータベース バックアップを構成できます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。

## 自動バックアップ設定

自動バックアップで構成できるオプションを次の表に示します。実際の構成手順は、Azure ポータルと Azure Windows PowerShell コマンドのどちらを使用するかによって異なります。

|設定|範囲 (既定値)|説明|
|---|---|---|
|**自動化されたバックアップ**|有効/無効 (無効)|SQL Server 2014 Standard または Enterprise を実行している Azure VM で、自動バックアップを有効または無効にします。|
|**保有期間**|1 ～ 30 日 (30 日)|バックアップを保持する日数。|
|**ストレージ アカウント**|Azure ストレージ アカウント (指定された VM 用に作成されたストレージ アカウント)|自動バックアップのファイルを BLOB ストレージに保存するために使用する Azure ストレージ アカウント。この場所にコンテナーが作成され、すべてのバックアップ ファイルが保存されます。バックアップ ファイルの名前付け規則には、日付、時刻、およびコンピューター名が含まれます。|
|**暗号化**|有効/無効 (無効)|暗号化を有効または無効にします。暗号化を有効にすると、バックアップの復元に使用する証明書は、指定されたストレージ アカウントの同じ automaticbackup コンテナー内に、同じ名前付け規則を使用して配置されます。パスワードが変更された場合、そのパスワードを使用して新しい証明書が生成されますが、以前のバックアップの復元には古い証明書が引き続き使用されます。|
|**パスワード**|パスワード テキスト (なし)|暗号化キーのパスワード。暗号化を有効にした場合にのみ必須となります。暗号化されたバックアップを復元するには、バックアップの作成時に使用した正しいパスワードおよび関連する証明書が必要です。|

## Azure ポータルでの自動バックアップの構成

新しい SQL Server 2014 仮想マシンを作成するときに、Azure ポータルを使用して、自動バックアップを構成できます。

>[AZURE.NOTE]自動バックアップは、SQL Server IaaS エージェントに依存します。このエージェントをインストールして構成するには、ターゲット仮想マシン上で Azure VM エージェントが実行されている必要があります。仮想マシン ギャラリーの最近のイメージでは、このオプションが既定で有効になっていますが、既存の VM では、Azure VM エージェントが存在しない可能性があります。独自の VM イメージを使用している場合は、SQL Server IaaS エージェントもインストールする必要があります。詳細については、「[VM エージェントと拡張機能 – パート 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)」をご覧ください。

次の Azure ポータルのスクリーンショットは、**[オプションの構成]** の **[SQL 自動バックアップ]** の各オプションを示しています。

![Azure ポータルの SQL 自動バックアップ構成](./media/virtual-machines-sql-server-automated-backup/IC778483.jpg)

既存の SQL Server 2014 仮想マシンでは、仮想マシンのプロパティの **[構成]** セクションで **[自動バックアップ]** 設定を選択します。**[自動化されたバックアップ]** ウィンドウでは、機能の有効化、保有期間の設定、ストレージ アカウントの選択、暗号化の設定を行うことができます。次のスクリーンショットにこれを示します。

![Azure ポータルの自動バックアップ構成](./media/virtual-machines-sql-server-automated-backup/IC792133.jpg)

>[AZURE.NOTE]自動バックアップを初めて有効にすると、バックグラウンドで SQL Server IaaS エージェントが構成されます。この間、Azure ポータルには自動バックアップが構成されていることは示されません。エージェントがインストールされ、構成されるまで数分待ちます。その後、Azure ポータルに新しい設定が反映されます。

## PowerShell を使用した自動バックアップの構成

次の PowerShell の例では、既存の SQL Server 2014 VM の自動バックアップを構成しています。**New-AzureVMSqlServerAutoBackupConfig** コマンドは、$storageaccount 変数で指定された Azure ストレージ アカウントにバックアップを保存するように、自動バックアップ設定を構成します。これらのバックアップは 10 日間保持されます。**Set-AzureVMSqlServerExtension** コマンドは、指定された Azure VM をこれらの設定で更新します。

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

SQL Server IaaS エージェントのインストールと構成には数分かかる場合があります。

暗号化を有効にするには、EnableEncryption パラメーターと、CertificatePassword パラメーターのパスワード (セキュリティで保護された文字列) を渡すように、前のスクリプトを変更します。次のスクリプトでは、前の例の自動バックアップ設定を有効にし、暗号化を追加します。

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

自動バックアップを無効にするには、**New-AzureVMSqlServerAutoBackupConfig** の **-Enable** パラメーターを指定せずに、同じスクリプトを実行します。インストールと同様に、自動バックアップの無効化には数分かかる場合があります。

## SQL Server IaaS エージェントの無効化とアンインストール

自動バックアップと自動修正で SQL Server IaaS エージェントを無効にするには、次のコマンドを使用します。

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

SQL Server IaaS エージェントをアンインストールするには、次の構文を使用します。

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

**Remove-AzureVMSqlServerExtension** コマンドを使用して、拡張機能をアンインストールすることもできます。

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

>[AZURE.NOTE]SQL Server IaaS エージェントを無効化またはアンインストールしても、以前に構成されたマネージ バックアップ設定は削除されません。SQL Server IaaS エージェントを無効化またはアンインストールする前に、自動バックアップを無効にしておく必要があります。

## 互換性

次の製品は、自動バックアップの SQL Server IaaS エージェント機能と互換性があります。

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2014 Standard

- SQL Server 2014 Enterprise

## 次のステップ

自動バックアップでは、Azure VM でマネージ バックアップが構成されます。そのため、[マネージ バックアップに関するドキュメント](https://msdn.microsoft.com/library/dn449496.aspx)を見直して、動作と影響を理解することが重要です。

Azure VM の SQL Server のバックアップと復元に関するその他のガイダンスについては、「[Azure の仮想マシンにおける SQL Server のバックアップと復元](virtual-machines-sql-server-backup-and-restore.md)」をご覧ください。

Azure の SQL Server VM の関連機能については、「[Azure Virtual Machines での SQL Server の自動修正](virtual-machines-sql-server-automated-patching.md)」をご覧ください。

[Azure Virtual Machines で SQL Server を実行するための他のリソース](virtual-machines-sql-server-infrastructure-services.md)を確認します。

<!---HONumber=AcomDC_1203_2015-->