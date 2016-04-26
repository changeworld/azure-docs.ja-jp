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
	ms.date="04/08/2016"
	ms.author="jroth" />

# Azure Virtual Machines での SQL Server の自動修正 (クラシック)

自動修正では、SQL Server 2012 または 2014 を実行している Azure 仮想マシンのメンテナンス期間が設定されます。このメンテナンス期間にのみ、自動更新プログラムをインストールできます。これにより、SQL Server では、システムの更新とこれに関連する再起動が、データベースに最適な時間帯に実行されるようになります。これは SQL Server IaaS エージェントに依存します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。

## Azure ポータルでの自動修正の構成

新しい SQL Server 仮想マシンを作成するときに、[Azure ポータル](http://go.microsoft.com/fwlink/?LinkID=525040&clcid=0x409)を使用して、自動修正を構成できます。

>[AZURE.NOTE] 自動修正は、SQL Server IaaS エージェントに依存します。このエージェントをインストールして構成するには、ターゲット仮想マシン上で Azure VM エージェントが実行されている必要があります。仮想マシン ギャラリーの最近のイメージでは、このオプションが既定で有効になっていますが、既存の VM では、Azure VM エージェントが存在しない可能性があります。独自の VM イメージを使用している場合は、SQL Server IaaS エージェントもインストールする必要があります。詳細については、「[VM エージェントと拡張機能 – パート 2](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)」をご覧ください。

次の Azure ポータルのスクリーンショットは、**[オプションの構成]** の **[SQL 自動修正]** の各オプションを示しています。

![Azure ポータルの SQL 自動修正](./media/virtual-machines-windows-classic-sql-automated-patching/IC778484.jpg)

既存の SQL Server 2012 または 2014 仮想マシンでは、仮想マシンのプロパティの **[構成]** セクションで **[自動修正]** 設定を選択します。**[自動修正]** ウィンドウでは、この機能の有効化、メンテナンス スケジュールと開始時間の設定、メンテナンス期間の選択を行うことができます。次のスクリーンショットにこれを示します。

![Azure ポータルの自動修正構成](./media/virtual-machines-windows-classic-sql-automated-patching/IC792132.jpg)

>[AZURE.NOTE] 自動修正を初めて有効にすると、バックグラウンドで SQL Server IaaS エージェントが構成されます。この間、Azure ポータルには自動修正が構成されていることは示されません。エージェントがインストールされ、構成されるまで数分待ちます。その後、Azure ポータルに新しい設定が反映されます。

## PowerShell を使用した自動修正の構成

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

## SQL Server IaaS エージェントの無効化とアンインストール

自動バックアップと自動修正で SQL Server IaaS エージェントを無効にするには、次のコマンドを使用します。

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

SQL Server IaaS エージェントをアンインストールするには、次の構文を使用します。

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

**Remove-AzureVMSqlServerExtension** コマンドを使用して、拡張機能をアンインストールすることもできます。

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

## 互換性

次の製品は、自動修正の SQL Server IaaS エージェント機能と互換性があります。

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2012

- SQL Server 2014

## 次のステップ

Azure の SQL Server VM の関連機能については、「[Azure Virtual Machines での SQL Server の自動バックアップ](virtual-machines-windows-classic-sql-automated-backup.md)」をご覧ください。

[Azure Virtual Machines で SQL Server を実行するための他のリソース](virtual-machines-windows-sql-server-iaas-overview.md)を確認します。

<!---HONumber=AcomDC_0413_2016-->