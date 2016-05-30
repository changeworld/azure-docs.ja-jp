<properties
	pageTitle="SQL Server VM 用 SQL Server Agent 拡張機能 (クラシック) | Microsoft Azure"
	description="このトピックでは、SQL Server Agent 拡張機能を管理して、SQL Server の特定の管理機能を自動化する方法について説明します。自動バックアップ、自動修正、および Azure Key Vault の統合が含まれます。このトピックでは、クラシック デプロイメント モードを使用します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/16/2016"
	ms.author="jroth"/>

# SQL Server VM 用 SQL Server Agent 拡張機能 (クラシック)

> [AZURE.SELECTOR]
- [リソース マネージャー](virtual-machines-windows-sql-server-agent-extension.md)
- [クラシック](virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS Agent 拡張機能 (SQLIaaSAgent) は、管理タスクを自動化するために Azure 仮想マシン上で実行されます。このトピックでは、この拡張機能によってサポートされるサービスの概要と、インストール、状態、および削除のための手順について説明します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。この記事の Resource Manager バージョンを確認するには、「[SQL Server VM 用 SQL Server Agent 拡張機能 (Resource Manager)](virtual-machines-windows-sql-server-agent-extension.md)」を参照してください。

## サポートされているサービス

SQL Server IaaS Agent 拡張機能は、次の管理タスクをサポートします。

| 管理機能 | 説明 |
|---------------------|-------------------------------|
| **SQL Automated Backup** | VM 内の SQL Server の既定のインスタンスについて、すべてのデータベースのバックアップを自動的にスケジュールします。詳細については、「[Azure Virtual Machines での SQL Server の自動バックアップ (クラシック)](virtual-machines-windows-classic-sql-automated-backup.md)」をご覧ください。|
| **SQL Automated Patching** | VM の更新プログラムを実行できるメンテナンス期間を構成します。これにより、ワークロードのピーク時の更新を回避できます。詳細については、「[Azure Virtual Machines での SQL Server の自動修正 (クラシック)](virtual-machines-windows-classic-sql-automated-patching.md)」を参照してください。|
| **Azure Key Vault の統合** | SQL Server VM に Azure Key Vault を自動的にインストールして構成できます。詳細については、「[Azure VM 上の SQL Server 向け Azure Key Vault 統合の構成 (クラシック)](virtual-machines-windows-classic-ps-sql-keyvault.md)」を参照してください。|

## 前提条件

VM で SQL Server IaaS Agent 拡張機能を使用するための要件:

- Azure VM ゲスト エージェント (BGInfo 拡張機能は新しい Azure VM に自動的にインストールされます)。
- Windows Server 2012、Windows Server 2012 R2、またはそれ以降。
- SQL Server 2012、SQL Server 2014、またはそれ以降。

Powershell コマンドレットを使用するための要件:

- 最新の Azure PowerShell ([ここから入手可能](../powershell-install-configure.md))。

## インストール

SQL Server IaaS Agent 拡張機能は、SQL Server 仮想マシン ギャラリー イメージのいずれかをプロビジョニングしたときに自動的にインストールされます。

OS 専用の Windows Server 仮想マシンを作成する場合は、**Set-AzureVMSqlServerExtension** PowerShell コマンドレットを使用して、拡張機能を手動でインストールできます。このコマンドを使用して、エージェントのいずれかのサービス (自動修正など) を構成します。VM は、エージェントがインストールされていない場合はそれをインストールします。

>[AZURE.NOTE] **Set-AzureVMSqlServerExtension** PowerShell の使用方法については、この記事の「[サポートされているサービス](#supported-services)」セクションを参照してください。

## 状態

拡張機能がインストールされていることを確認する 1 つの方法は、Azure ポータルにエージェントの状態を表示することです。仮想マシンのブレードで **[すべての設定]** を選択し、**[拡張機能]** をクリックします。**SQLIaaSAgent** 拡張機能が表示されます。

![Azure ポータルでの SQL Server IaaS Agent 拡張機能](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

**Get-AzureVMSqlServerExtension** Azure Powershell コマンドレットを使用することもできます。

	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## 削除   

Azure ポータルで、仮想マシンのプロパティの **[拡張機能]** ブレード上の省略記号をクリックすることで拡張機能をアンインストールできます。その後、[**削除**] をクリックします。

![Azure ポータルで SQL Server IaaS Agent 拡張機能をアンインストールします](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

**Remove-AzureVMSqlServerExtension** Azure Powershell コマンドレットを使用することもできます。

	Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## 次のステップ

拡張機能によってサポートされるいずれかのサービスの使用を開始します。詳細については、この記事の「[サポートされているサービス](#supported-services)」を参照してください。

Azure Virtual Machines で SQL Server を実行する方法の詳細については、「[Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

<!---HONumber=AcomDC_0518_2016-->