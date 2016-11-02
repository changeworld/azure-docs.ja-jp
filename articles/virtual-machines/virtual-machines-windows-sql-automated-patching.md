<properties
	pageTitle="SQL Server VM の自動修正 (Resource Manager) |Microsoft Azure"
	description="Azure で実行されている SQL Server Virtual Machines に対する、Resource Manager を使用した自動修正機能について説明します。"
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
	ms.date="08/19/2016"
	ms.author="jroth" />

# Azure Virtual Machines での SQL Server の自動修正 (Resource Manager)

> [AZURE.SELECTOR]
- [リソース マネージャー](virtual-machines-windows-sql-automated-patching.md)
- [クラシック](virtual-machines-windows-classic-sql-automated-patching.md)

自動修正では、SQL Server を実行している Azure 仮想マシンのメンテナンス期間が設定されます。このメンテナンス期間にのみ、自動更新プログラムをインストールできます。これにより、SQL Server では、システムの更新とこれに関連する再起動が、データベースに最適な時間帯に実行されるようになります。自動修正は、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に依存します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] クラシック デプロイメント モデル。この記事のクラシック バージョンを確認するには、「[Azure Virtual Machines での SQL Server の自動修正 クラシック](virtual-machines-windows-classic-sql-automated-patching.md)」を参照してください。

## 前提条件

自動修正を使用するには、次の前提条件を検討してください。

**オペレーティング システム**:

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server のバージョン**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [最新の Azure PowerShell コマンドをインストールします](../powershell-install-configure.md) (PowerShell で自動修正を構成する場合)。

>[AZURE.NOTE] 自動修正は、SQL Server IaaS Agent 拡張機能に依存します。現在の SQL 仮想マシン ギャラリー イメージでは、既定でこの拡張機能が追加されます。詳細については、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に関するページをご覧ください。

## Settings

自動修正で構成できるオプションを次の表に示します。実際の構成手順は、Azure ポータルと Azure Windows PowerShell コマンドのどちらを使用するかによって異なります。

|設定|指定できる値|説明|
|---|---|---|
|**自動修正**|有効/無効 (無効)|Azure 仮想マシンの自動修正を有効または無効にします。|
|**メンテナンス スケジュール**|毎日、月曜日、火曜日、水曜日、木曜日、金曜日、土曜日、日曜日|仮想マシンの Windows、SQL Server、および Microsoft の更新プログラムをダウンロードしてインストールするスケジュール。|
|**メンテナンスの開始時間**|0 ～ 24|仮想マシンを更新するローカルの開始時刻。|
|**メンテナンス時間**|30 ～ 180|更新プログラムのダウンロードとインストールを完了するのに許可されている時間 (分単位)|
|**パッチのカテゴリ**|重要:|ダウンロードしてインストールする更新プログラムのカテゴリ。|

## ポータルでの構成
Azure ポータルを使用して、プロビジョニング中または既存の VM 用に、自動修正を構成することができます。

### 新しい VM
Resource Manager デプロイメント モデルで新しい SQL Server 仮想マシンを作成するときに、Azure ポータルを使用して、自動修正を構成します。

**[SQL Server の設定]** ブレードで、**[自動修正]** を選択します。次の Azure ポータルのスクリーンショットは、**[SQL 自動修正]** ブレードを示しています。

![Azure ポータルでの SQL 自動修正](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

コンテキストについては、[Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)に関するページのトピックをご覧ください。

### 既存の VM
既存の SQL Server 仮想マシンの場合は、ご使用の SQL Server 仮想マシンを選択します。**[設定]** ブレードの **[SQL Server の構成]** セクションを選択します。

![既存の VM の SQL 自動修正](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

**[SQL Server の構成]** ブレードの [自動修正] セクションで **[編集]** ボタンをクリックします。

![既存の VM の SQL 自動修正を構成する](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

終了したら、**[SQL Server の構成]** ブレードの下部にある **[OK]** ボタンをクリックして変更を保存します。

自動修正を初めて有効にすると、バックグラウンドで SQL Server IaaS Agent が構成されます。この間、自動修正が構成されていることは、Azure ポータルに示されない可能性があります。エージェントがインストールされ、構成されるまで数分待ちます。その後、Azure ポータルに新しい設定が反映されます。

>[AZURE.NOTE] テンプレートを使用して自動修正を構成することもできます。詳細については、[自動修正用の Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update)に関するページをご覧ください。

## PowerShell での構成

SQL VM をプロビジョニングしたら、PowerShell を使用して自動修正を構成します。

次の例では、PowerShell を使用して、既存の SQL Server VM で自動修正を構成しています。**AzureRM.Compute\\New-AzureVMSqlServerAutoPatchingConfig** コマンドは、自動更新の新しいメンテナンス期間を構成します。

	$vmname = "vmname"
	$resourcegroupname = "resourcegroupname"
	$aps = AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

この例に基づいて、対象の Azure VM への実際の影響を次の表に示します。

|パラメーター|効果|
|---|---|
|**DayOfWeek**|毎週木曜日に修正プログラムがインストールされます。|
|**MaintenanceWindowStartingHour**|午前 11 時に更新が開始されます。|
|**MaintenanceWindowsDuration**|修正プログラムを 120 分以内にインストールする必要があります。開始時刻に基づき、修正プログラムのインストールは午後 1 時までに完了する必要があります。|
|**PatchCategory**|このパラメーターに指定可能な設定は **Important** だけです。|

SQL Server IaaS エージェントのインストールと構成には数分かかる場合があります。

自動修正を無効にするには、**AzureRM.Compute\\New-AzureVMSqlServerAutoPatchingConfig** の **-Enable** パラメーターを指定せずに、同じスクリプトを実行します。**-Enable** パラメーターがない場合は、機能を無効にするコマンドが伝えられます。

## 次のステップ

その他の利用可能なオートメーション タスクについては、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に関するページをご覧ください。

Azure VM で SQL Server を実行する方法の詳細については、[Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)に関するページをご覧ください。

<!---HONumber=AcomDC_0824_2016-->