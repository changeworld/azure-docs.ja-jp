---
title: SQL Server VM 用 SQL Server Agent 拡張機能 (クラシック) | Microsoft Docs
description: このトピックでは、SQL Server Agent 拡張機能を管理して、SQL Server の特定の管理機能を自動化する方法について説明します。 自動バックアップ、自動修正、および Azure Key Vault の統合が含まれます。 このトピックでは、クラシック デプロイ モードを使用します。
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: jhubbard
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: jroth

---
# <a name="sql-server-agent-extension-for-sql-server-vms-classic"></a>SQL Server VM 用 SQL Server Agent 拡張機能 (クラシック)
> [!div class="op_single_selector"]
> * [リソース マネージャー](virtual-machines-windows-sql-server-agent-extension.md)
> * [クラシック](virtual-machines-windows-classic-sql-server-agent-extension.md)
> 
> 

SQL Server IaaS Agent 拡張機能 (SQLIaaSAgent) は、管理タスクを自動化するために Azure 仮想マシン上で実行されます。 このトピックでは、この拡張機能によってサポートされるサービスの概要と、インストール、状態、および削除のための手順について説明します。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

この記事の Resource Manager バージョンを確認するには、「 [SQL Server VM 用 SQL Server Agent 拡張機能 (Resource Manager)](virtual-machines-windows-sql-server-agent-extension.md)」を参照してください。

## <a name="supported-services"></a>サポートされているサービス
SQL Server IaaS Agent 拡張機能は、次の管理タスクをサポートします。

| 管理機能 | 説明 |
| --- | --- |
| **SQL Automated Backup** |VM 内の SQL Server の既定のインスタンスについて、すべてのデータベースのバックアップを自動的にスケジュールします。 詳細については、「 [Azure Virtual Machines での SQL Server の自動バックアップ (クラシック)](virtual-machines-windows-classic-sql-automated-backup.md)」をご覧ください。 |
| **SQL Automated Patching** |VM の更新プログラムを実行できるメンテナンス期間を構成します。これにより、ワークロードのピーク時の更新を回避できます。 詳細については、「[Azure Virtual Machines での SQL Server の自動修正 (クラシック)](virtual-machines-windows-classic-sql-automated-patching.md)」を参照してください。 |
| **Azure Key Vault の統合** |SQL Server VM に Azure Key Vault を自動的にインストールして構成できます。 詳細については、「 [Azure VM 上の SQL Server 向け Azure Key Vault 統合の構成 (クラシック)](virtual-machines-windows-classic-ps-sql-keyvault.md)」を参照してください。 |

## <a name="prerequisites"></a>前提条件
VM で SQL Server IaaS Agent 拡張機能を使用するための要件:

### <a name="operating-system"></a>オペレーティング システム:
* Windows Server 2012
* Windows Server 2012 R2

### <a name="sql-server-versions"></a>SQL Server のバージョン:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[最新の Azure PowerShell コマンドをダウンロードして構成します](../powershell-install-configure.md)。

Windows PowerShell を起動し、 **Add-AzureAccount** コマンドを使用して Azure サブスクリプションに接続します。

    Add-AzureAccount

複数のサブスクリプションがある場合は、 **Select-AzureSubscription** を使用して、対象となるクラシック VM が含まれるサブスクリプションを選択します。

    Select-AzureSubscription -SubscriptionName <subscriptionname>

この時点で、 **Get-AzureVM** コマンドにより、クラシック仮想マシンとそれに関連付けられているサービス名の一覧を取得できます。

    Get-AzureVM

## <a name="installation"></a>インストール
クラシック VM の場合は、PowerShell を使用して、SQL Server IaaS Agent 拡張機能のインストールと、関連付けられているサービスの構成を行う必要があります。 拡張機能をインストールするには、 **Set-AzureVMSqlServerExtension** PowerShell コマンドレットを使用します。 たとえば、次のコマンドは、Windows Server VM (クラシック) に拡張機能をインストールし、"SQLIaaSExtension" という名前を付けます。

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

最新バージョンの SQL IaaS Agent 拡張機能に更新する場合は、拡張機能の更新後、仮想マシンを再起動する必要があります。

> [!NOTE]
> クラシック仮想マシンの場合、ポータルを通じて SQL IaaS Agent 拡張機能のインストールや構成を行うためのオプションはありません。
> 
> 

## <a name="status"></a>状態
拡張機能がインストールされていることを確認する 1 つの方法は、Azure ポータルにエージェントの状態を表示することです。 仮想マシンのブレードで **[すべての設定]** を選択し、**[拡張機能]** をクリックします。 **SQLIaaSAgent** 拡張機能が表示されます。

![Azure ポータルでの SQL Server IaaS Agent 拡張機能](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

**Get-AzureVMSqlServerExtension** Azure Powershell コマンドレットを使用することもできます。

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>削除
Azure ポータルで拡張機能をアンインストールするには、仮想マシンのプロパティの **[拡張機能]** ブレードにある省略記号をクリックします。 その後、 **[削除]**をクリックします。

![Azure ポータルで SQL Server IaaS Agent 拡張機能をアンインストールします](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

**Remove-AzureVMSqlServerExtension** Powershell コマンドレットを使用することもできます。

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>次のステップ
拡張機能によってサポートされるいずれかのサービスの使用を開始します。 詳細については、この記事の「 [サポートされているサービス](#supported-services) 」で参照されているトピックをご覧ください。

Azure Virtual Machines で SQL Server を実行する方法の詳細については、「 [Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

<!---HONumber=Oct16_HO2-->


