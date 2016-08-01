<properties
	pageTitle="SQL Server VM の自動修正 (クラシック) |Microsoft Azure"
	description="Azure でクラシック デプロイメント モデルを使用して実行されている SQL Server Virtual Machines の自動修正機能について説明します。"
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="07/15/2016"
	ms.author="jroth" />

# Azure Virtual Machines での SQL Server の自動修正 (クラシック)

> [AZURE.SELECTOR]
- [リソース マネージャー](virtual-machines-windows-sql-automated-patching.md)
- [クラシック](virtual-machines-windows-classic-sql-automated-patching.md)

自動修正では、SQL Server を実行している Azure 仮想マシンのメンテナンス期間が設定されます。このメンテナンス期間にのみ、自動更新プログラムをインストールできます。これにより、SQL Server では、システムの更新とこれに関連する再起動が、データベースに最適な時間帯に実行されるようになります。自動修正は、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-classic-sql-server-agent-extension.md)に依存します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 
この記事の Resource Manager バージョンを確認するには、[Azure Virtual Machines での SQL Server の自動修正 (Resource Manager)](virtual-machines-windows-sql-automated-patching.md) に関するページをご覧ください。

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

>[AZURE.NOTE] 自動修正は、SQL Server IaaS Agent 拡張機能に依存します。現在の SQL 仮想マシン ギャラリー イメージでは、既定でこの拡張機能が追加されます。詳細については、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-classic-sql-server-agent-extension.md)に関するページをご覧ください。

## Settings

自動修正で構成できるオプションを次の表に示します。実際の構成手順は、Azure ポータルと Azure Windows PowerShell コマンドのどちらを使用するかによって異なります。

|設定|指定できる値|説明|
|---|---|---|
|**自動修正**|有効/無効 (無効)|Azure 仮想マシンの自動修正を有効または無効にします。|
|**メンテナンス スケジュール**|毎日、月曜日、火曜日、水曜日、木曜日、金曜日、土曜日、日曜日|仮想マシンの Windows、SQL Server、および Microsoft の更新プログラムをダウンロードしてインストールするスケジュール。|
|**メンテナンスの開始時間**|0 ～ 24|仮想マシンを更新するローカルの開始時刻。|
|**メンテナンス時間**|30 ～ 180|更新プログラムのダウンロードとインストールを完了するのに許可されている時間 (分単位)|
|**パッチのカテゴリ**|重要|ダウンロードしてインストールする更新プログラムのカテゴリ。|

## ポータルでの構成
Azure ポータルを使用して、プロビジョニング中または既存の VM 用に、自動修正を構成することができます。

### 新しい VM
クラシック デプロイメント モデルで新しい SQL Server 仮想マシンを作成するときに、Azure ポータルを使用して、自動修正を構成します。

次の Azure ポータルのスクリーンショットは、**[オプションの構成]** の **[SQL 自動修正]** の各オプションを示しています。

![Azure ポータルの SQL 自動修正](./media/virtual-machines-windows-classic-sql-automated-patching/IC778484.jpg)

### 既存の VM
既存の SQL Server 2012 または 2014 仮想マシンでは、仮想マシンのプロパティの **[構成]** セクションで **[自動修正]** 設定を選択します。**[自動修正]** ウィンドウでは、この機能の有効化、メンテナンス スケジュールと開始時間の設定、メンテナンス期間の選択を行うことができます。次のスクリーンショットにこれを示します。

![Azure ポータルの自動修正構成](./media/virtual-machines-windows-classic-sql-automated-patching/IC792132.jpg)

>[AZURE.NOTE] 自動修正を初めて有効にすると、バックグラウンドで SQL Server IaaS エージェントが構成されます。この間、自動修正が構成されていることは、Azure ポータルに示されない可能性があります。エージェントがインストールされ、構成されるまで数分待ちます。その後、Azure ポータルに新しい設定が反映されます。

## PowerShell での構成

PowerShell を使用して自動修正を構成することもできます。

次の例では、PowerShell を使用して、既存の SQL Server VM で自動修正を構成しています。**New-AzureVMSqlServerAutoPatchingConfig** コマンドは、自動更新の新しいメンテナンス期間を構成します。

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

この例に基づいて、対象の Azure VM への実際の影響を次の表に示します。

|パラメーター|効果|
|---|---|
|**DayOfWeek**|毎週木曜日に修正プログラムがインストールされます。|
|**MaintenanceWindowStartingHour**|午前 11 時に更新が開始されます。|
|**MaintenanceWindowsDuration**|修正プログラムを 120 分以内にインストールする必要があります。開始時刻に基づき、修正プログラムのインストールは午後 1 時までに完了する必要があります。|
|**PatchCategory**|このパラメーターに指定可能な設定は "Important" だけです。|

SQL Server IaaS エージェントのインストールと構成には数分かかる場合があります。

自動修正を無効にするには、New-AzureVMSqlServerAutoPatchingConfig の -Enable パラメーターを指定せずに、同じスクリプトを実行します。インストールと同様に、自動修正の無効化には数分かかる場合があります。

## 次のステップ

その他の利用可能な自動タスクについては、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-classic-sql-server-agent-extension.md)に関するページをご覧ください。

Azure VM で SQL Server を実行する方法の詳細については、「[Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)」をご覧ください。

<!---HONumber=AcomDC_0720_2016-->