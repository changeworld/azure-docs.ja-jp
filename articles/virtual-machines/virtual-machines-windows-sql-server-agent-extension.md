<properties
	pageTitle="SQL Server VM 用 SQL Server Agent 拡張機能 (Resource Manager) | Microsoft Azure"
	description="このトピックでは、SQL Server Agent 拡張機能を管理して、SQL Server の特定の管理機能を自動化する方法について説明します。自動バックアップ、自動修正、および Azure Key Vault の統合が含まれます。この記事では、Resource Manager デプロイ モードを使用します。"
	services="virtual-machines-windows"
	documentationCenter=""
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
	ms.author="jroth"/>

# SQL Server VM 用 SQL Server Agent 拡張機能 (Resource Manager)

> [AZURE.SELECTOR]
- [リソース マネージャー](virtual-machines-windows-sql-server-agent-extension.md)
- [クラシック](virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS Agent 拡張機能 (SQLIaaSExtension) は、管理タスクを自動化するために Azure 仮想マシン上で実行されます。このトピックでは、この拡張機能によってサポートされるサービスの概要と、インストール、状態、および削除のための手順について説明します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] クラシック デプロイ モデル。この記事のクラシック バージョンを確認するには、「[SQL Server IaaS Agent 拡張機能 クラシック デプロイ](virtual-machines-windows-classic-sql-server-agent-extension.md)」を参照してください。

## サポートされているサービス

SQL Server IaaS Agent 拡張機能は、次の管理タスクをサポートします。

| 管理機能 | 説明 |
|---------------------|-------------------------------|
| **SQL Automated Backup** | VM 内の SQL Server の既定のインスタンスについて、すべてのデータベースのバックアップを自動的にスケジュールします。詳細については、「[Automated backup for SQL Server in Azure Virtual Machines (Resource Manager) (Azure Virtual Machines での SQL Server の自動バックアップ (Resource Manager))](virtual-machines-windows-sql-automated-backup.md)」を参照してください。|
| **SQL Automated Patching** | VM の更新プログラムを実行できるメンテナンス期間を構成します。これにより、ワークロードのピーク時の更新を回避できます。詳細については、「[Automated patching for SQL Server in Azure Virtual Machines (Resource Manager) (Azure Virtual Machines での SQL Server の自動修正 (Resource Manager))](virtual-machines-windows-sql-automated-patching.md)」を参照してください。|
| **Azure Key Vault の統合** | SQL Server VM に Azure Key Vault を自動的にインストールして構成できます。詳細については、「[Azure VM で SQL Server 用に Azure Key Vault 統合を構成する (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md)」を参照してください。|

## 前提条件

VM で SQL Server IaaS Agent 拡張機能を使用するための要件:

**オペレーティング システム**:

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server のバージョン**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [最新の Azure PowerShell コマンドをダウンロードして構成します](../powershell-install-configure.md)

## インストール

SQL Server IaaS Agent 拡張機能は、SQL Server 仮想マシン ギャラリー イメージのいずれかをプロビジョニングしたときに自動的にインストールされます。

OS 専用の Windows Server 仮想マシンを作成する場合は、**Set-AzureVMSqlServerExtension** PowerShell コマンドレットを使用して、拡張機能を手動でインストールできます。たとえば、次のコマンドは、OS 専用の Windows Server VM に拡張機能をインストールし、"SQLIaaSExtension" という名前を付けます。

	Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2"

最新バージョンの SQL IaaS Agent 拡張機能に更新する場合は、拡張機能の更新後、仮想マシンを再起動する必要があります。

>[AZURE.NOTE] SQL Server IaaS Agent 拡張機能を Windows Server VM で手動でインストールする場合は、PowerShell コマンドを使用して、その機能を使用および管理する必要があります。ポータルのインターフェイスは、SQL Server のギャラリー イメージに対してのみ使用できます。

## 状態

拡張機能がインストールされていることを確認する 1 つの方法は、Azure ポータルにエージェントの状態を表示することです。仮想マシンのブレードで **[すべての設定]** を選択し、**[拡張機能]** をクリックします。**SQLIaaSExtension** 拡張機能が表示されます。

![Azure ポータルでの SQL Server IaaS Agent 拡張機能](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

**Get-AzureVMSqlServerExtension** Azure Powershell コマンドレットを使用することもできます。

	Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

前のコマンドは、エージェントがインストールされていることを確認し、全般的なステータス情報を提供します。次のコマンドを使用して、自動バックアップと自動修正に関する特定のステータス情報を取得することもできます。

	$sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
	$sqlext.AutoPatchingSettings
	$sqlext.AutoBackupSettings

## 削除   

Azure ポータルで拡張機能をアンインストールするには、仮想マシンのプロパティの **[拡張機能]** ブレードにある省略記号をクリックします。その後、**[削除]** をクリックします。

![Azure ポータルで SQL Server IaaS Agent 拡張機能をアンインストールします](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

**Remove-AzureRmVMSqlServerExtension** Powershell コマンドレットを使用することもできます。

	Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## 次のステップ

拡張機能によってサポートされるいずれかのサービスの使用を開始します。詳細については、この記事の「[サポートされているサービス](#supported-services)」で参照されているトピックをご覧ください。

Azure Virtual Machines で SQL Server を実行する方法の詳細については、「[Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

<!---HONumber=AcomDC_0720_2016-->
