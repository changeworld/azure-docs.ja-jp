<properties
	pageTitle="SQL Server Virtual Machines の自動バックアップ (Resource Manager) | Microsoft Azure"
	description="Azure Virtual Machines で実行されている SQL Server に対する、Resource Manager を使用した自動バックアップ機能について説明します。"
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="07/14/2016"
	ms.author="jroth" />

# Azure Virtual Machines での SQL Server の自動バックアップ (Resource Manager)

> [AZURE.SELECTOR]
- [リソース マネージャー](virtual-machines-windows-sql-automated-backup.md)
- [クラシック](virtual-machines-windows-classic-sql-automated-backup.md)

自動バックアップでは、SQL Server 2014 Standard または Enterprise を実行する Azure VM 上の既存のデータベースと新しいデータベースのすべてを対象に、[Microsoft Azure へのマネージ バックアップ](https://msdn.microsoft.com/library/dn449496.aspx)が自動的に構成されます。これにより、永続的な Azure BLOB ストレージを利用した日常的なデータベース バックアップを構成できます。自動バックアップは、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に依存します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] クラシック デプロイ モデル。この記事のクラシック バージョンを確認するには、「[Azure Virtual Machines での SQL Server の自動バックアップ クラシック](virtual-machines-windows-classic-sql-automated-backup.md)」を参照してください。

## 前提条件

自動バックアップを使用するには、次の前提条件を検討してください。

**オペレーティング システム**:

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server バージョン/エディション**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

**データベースの構成**:

- ターゲット データベースでは、完全復旧モデルを使用する必要があります

**Azure PowerShell**:

- [最新の Azure PowerShell コマンドをインストールします](../powershell-install-configure.md) (PowerShell で自動バックアップを構成する場合)。

>[AZURE.NOTE] 自動バックアップは、SQL Server IaaS Agent 拡張機能に依存します。現在の SQL 仮想マシン ギャラリー イメージでは、既定でこの拡張機能が追加されます。詳細については、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に関するページをご覧ください。

## Settings

自動バックアップで構成できるオプションを次の表に示します。実際の構成手順は、Azure ポータルと Azure Windows PowerShell コマンドのどちらを使用するかによって異なります。

|設定|範囲 (既定値)|説明|
|---|---|---|
|**自動化されたバックアップ**|有効/無効 (無効)|SQL Server 2014 Standard または Enterprise を実行している Azure VM で、自動バックアップを有効または無効にします。|
|**保有期間**|1 ～ 30 日 (30 日)|バックアップを保持する日数。|
|**ストレージ アカウント**|Azure ストレージ アカウント (指定された VM 用に作成されたストレージ アカウント)|自動バックアップのファイルを BLOB ストレージに保存するために使用する Azure ストレージ アカウント。この場所にコンテナーが作成され、すべてのバックアップ ファイルが保存されます。バックアップ ファイルの名前付け規則には、日付、時刻、およびコンピューター名が含まれます。|
|**暗号化**|有効/無効 (無効)|暗号化を有効または無効にします。暗号化を有効にすると、バックアップの復元に使用する証明書は、指定されたストレージ アカウントの同じ automaticbackup コンテナー内に、同じ名前付け規則を使用して配置されます。パスワードが変更された場合、そのパスワードを使用して新しい証明書が生成されますが、以前のバックアップの復元には古い証明書が引き続き使用されます。|
|**パスワード**|パスワード テキスト (なし)|暗号化キーのパスワード。暗号化を有効にした場合にのみ必須となります。暗号化されたバックアップを復元するには、バックアップの作成時に使用した正しいパスワードおよび関連する証明書が必要です。|

## ポータルでの構成
Azure ポータルを使用して、プロビジョニング中または既存の VM 用に、自動バックアップを構成することができます。

### 新しい VM
Resource Manager デプロイメント モデルで新しい SQL Server 2014 仮想マシンを作成するときに、Azure ポータルを使用して、自動バックアップを構成します。

**[SQL Server の設定]** ブレードで、**[自動バックアップ]** を選択します。次の Azure ポータルのスクリーンショットは、**[SQL 自動バックアップ]** ブレードを示しています。

![Azure ポータルの SQL 自動バックアップ構成](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

コンテキストについては、[Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)に関するトピックをご覧ください。

### 既存の VM
既存の SQL Server 仮想マシンの場合は、ご使用の SQL Server 仮想マシンを選択します。**[設定]** ブレードの **[SQL Server の構成]** セクションを選択します。

![既存の VM の SQL 自動バックアップ](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

**[SQL Server の構成]** ブレードの [自動バックアップ] セクションで **[編集]** ボタンをクリックします。

![既存の VM の SQL 自動バックアップを構成する](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

終了したら、**[SQL Server の構成]** ブレードの下部にある **[OK]** ボタンをクリックして変更を保存します。

自動バックアップを初めて有効にすると、バックグラウンドで SQL Server IaaS エージェントが構成されます。この間、自動バックアップが構成されていることは、Azure ポータルに示されない可能性があります。エージェントがインストールされ、構成されるまで数分待ちます。その後、Azure ポータルに新しい設定が反映されます。

>[AZURE.NOTE] テンプレートを使用して自動バックアップを構成することもできます。詳細については、[自動バックアップ用の Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)に関するページをご覧ください。

## PowerShell での構成

SQL VM をプロビジョニングしたら、PowerShell を使用して自動バックアップを構成します。

次の PowerShell の例では、既存の SQL Server 2014 VM の自動バックアップを構成しています。**AzureRM.Compute\\New-AzureVMSqlServerAutoBackupConfig** コマンドは、仮想マシンに関連付けられた Azure ストレージ アカウントにバックアップを保存するように、自動バックアップ設定を構成します。これらのバックアップは 10 日間保持されます。**Set-AzureRmVMSqlServerExtension** コマンドは、指定された Azure VM をこれらの設定で更新します。

	$vmname = "vmname"
	$resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

SQL Server IaaS エージェントのインストールと構成には数分かかる場合があります。

暗号化を有効にするには、**EnableEncryption** パラメーターと、**CertificatePassword** パラメーターのパスワード (セキュリティで保護された文字列) を渡すように、前のスクリプトを変更します。次のスクリプトでは、前の例の自動バックアップ設定を有効にし、暗号化を追加します。

	$vmname = "vmname"
	$resourcegroupname = "resourcegroupname"
	$password = "P@ssw0rd"
	$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
	$autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

	Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

自動バックアップを無効にするには、**AzureRM.Compute\\New-AzureVMSqlServerAutoBackupConfig** コマンドの **-Enable** パラメーターを指定せずに、同じスクリプトを実行します。**-Enable** パラメーターがない場合は、機能を無効にするコマンドが伝えられます。インストールと同様に、自動バックアップの無効化には数分かかる場合があります。

>[AZURE.NOTE] SQL Server IaaS Agent を削除しても、前に構成された自動バックアップ設定は削除されません。SQL Server IaaS エージェントを無効化またはアンインストールする前に、自動バックアップを無効にしておく必要があります。

## 次のステップ

自動バックアップでは、Azure VM でマネージ バックアップが構成されます。そのため、[マネージ バックアップに関するドキュメント](https://msdn.microsoft.com/library/dn449496.aspx)を見直して、動作と影響を理解することが重要です。

Azure VM の SQL Server のバックアップと復元に関するその他のガイダンスについては、「[Azure の仮想マシンにおける SQL Server のバックアップと復元](virtual-machines-windows-sql-backup-recovery.md)」をご覧ください。

その他の利用可能なオートメーション タスクについては、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に関するページをご覧ください。

Azure VM で SQL Server を実行する方法の詳細については、[Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)に関するページをご覧ください。

<!---HONumber=AcomDC_0720_2016-->
