---
title: SQL VM (Resource Manager) での管理タスクの自動化 | Microsoft Docs
description: この記事では、SQL Server エージェント拡張機能を管理して、SQL Server の特定の管理機能を自動化する方法について説明します。 自動バックアップ、自動修正、および Azure Key Vault の統合が含まれます。
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: jroth
ms.openlocfilehash: c663aec02d4d1808426a9f05a6674d5504563a63
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39012260"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>SQL Server Agent 拡張機能 (Resource Manager) による Azure Virtual Machines での管理タスクの自動化
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [クラシック](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS Agent 拡張機能 (SQLIaaSExtension) は、管理タスクを自動化するために Azure 仮想マシン上で実行されます。 この記事では、この拡張機能によってサポートされるサービスの概要と、インストール、状態、および削除のための手順について説明します。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

この記事のクラシック バージョンを確認するには、「[SQL Server VM 用 SQL Server Agent 拡張機能 (クラシック)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)」をご覧ください。

## <a name="supported-services"></a>サポートされているサービス
SQL Server IaaS Agent 拡張機能は、次の管理タスクをサポートします。

| 管理機能 | 説明 |
| --- | --- |
| **SQL Automated Backup** |VM 内の SQL Server の既定のインスタンスについて、すべてのデータベースのバックアップを自動的にスケジュールします。 詳細については、「[Azure Virtual Machines での SQL Server の自動バックアップ (Resource Manager)](virtual-machines-windows-sql-automated-backup.md)」をご覧ください。 |
| **SQL Automated Patching** |VM で重要な更新プログラムを実行できるメンテナンス期間を構成します。これにより、ワークロードのピーク時の更新を回避できます。 詳細については、「[Azure Virtual Machines での SQL Server の自動修正 (Resource Manager)](virtual-machines-windows-sql-automated-patching.md)」をご覧ください。 |
| **Azure Key Vault の統合** |SQL Server VM に Azure Key Vault を自動的にインストールして構成できます。 詳細については、「[Azure VM で SQL Server 用に Azure Key Vault 統合を構成する (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md)」をご覧ください。 |

SQL Server IaaS Agent 拡張機能をインストールして実行すると、Azure ポータル内の仮想マシンの SQL Server パネルや、SQL Server marketplace イメージ用の Azure PowerShell、拡張機能のインストール用の Azure PowerShell などを介して、これらの管理機能を使用できるようになります。 

## <a name="prerequisites"></a>前提条件
VM で SQL Server IaaS Agent 拡張機能を使用するための要件:

**オペレーティング システム**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server のバージョン**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [最新の Azure PowerShell コマンドをダウンロードして構成します](/powershell/azure/overview)

> [!IMPORTANT]
> 現時点で、Azure 上の SQL Server FCI では [SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)がサポートされていません。 FCI に参加している VM からこの拡張機能をアンインストールすることをお勧めします。 エージェントのアンインストール後、この拡張機能によってサポートされる機能を SQL VM で使用することはできません。

## <a name="installation"></a>インストール
SQL Server IaaS Agent 拡張機能は、SQL Server 仮想マシン ギャラリー イメージのいずれかをプロビジョニングしたときに自動的にインストールされます。 これらの SQL Server VM の 1 つに拡張機能を手動で再インストール必要がある場合は、次の PowerShell コマンドを使用します。

```powershell
Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"
```

> [!IMPORTANT]
> 拡張機能がまだインストールされていない場合、拡張機能をインストールすると、SQL Server サービスが再起動されます。

> [!NOTE]
> SQL Server IaaS Agent 拡張機能は、[SQL Server VM ギャラリー イメージ](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (従量課金制またはライセンス持ち込み) でのみサポートされます。 SQL Server を OS のみの Windows Server 仮想マシンに手動でインストールする場合や、カスタマイズされた SQL Server VM VHD をデプロイする場合、この拡張機能はサポートされません。 そのような場合は、PowerShell を使用して拡張機能を手動でインストールおよび管理できる可能性がありますが、Azure Portal には SQL Server の構成設定が表示されません。 ただし、代わりに SQL Server VM ギャラリー イメージをインストールしてカスタマイズすることを強くお勧めします。

## <a name="status"></a>Status
拡張機能がインストールされていることを確認する 1 つの方法は、Azure ポータルにエージェントの状態を表示することです。 仮想マシンのウィンドウで **[すべての設定]** を選択し、**[拡張機能]** をクリックします。 **SQLIaaSExtension** 拡張機能が表示されます。

![Azure ポータルでの SQL Server IaaS Agent 拡張機能](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

**Get-AzureRmVMSqlServerExtension** Azure PowerShell コマンドレットを使用することもできます。

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

前のコマンドは、エージェントがインストールされていることを確認し、全般的なステータス情報を提供します。 次のコマンドを使用して、自動バックアップと自動修正に関する特定のステータス情報を取得することもできます。

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>削除
Azure Portal で拡張機能をアンインストールするには、仮想マシンのプロパティの **[拡張機能]** ウィンドウにある省略記号をクリックします。 その後、 **[削除]** をクリックします。

![Azure ポータルで SQL Server IaaS Agent 拡張機能をアンインストールする](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

**Remove-AzureRmVMSqlServerExtension** PowerShell コマンドレットを使用することもできます。

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>次の手順
拡張機能によってサポートされるいずれかのサービスの使用を開始します。 詳細については、この記事の「[サポートされているサービス](#supported-services) 」で参照されているトピックをご覧ください。

Azure Virtual Machines で SQL Server を実行する方法の詳細については、「 [Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

