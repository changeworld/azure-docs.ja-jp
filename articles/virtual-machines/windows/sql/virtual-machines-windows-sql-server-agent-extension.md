---
title: SQL Server IaaS Agent 拡張機能を使用して Azure 仮想マシン上で管理タスクを自動化する | Microsoft Docs
description: この記事では、SQL Server IaaS Agent 拡張機能を管理して、SQL Server の特定の管理タスクを自動化する方法について説明します。 これらには、自動バックアップ、自動修正、および Azure Key Vault の統合が含まれます。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f4dd529481a6216e43d35c76ecee734543d487f3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100476"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>SQL Server IaaS Agent 拡張機能を使用して Azure 仮想マシン上で管理タスクを自動化する
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [クラシック](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS Agent 拡張機能 (SQLIaaSExtension) は、管理タスクを自動化するために Azure 仮想マシン上で実行されます。 この記事では、拡張機能でサポートされているサービスの概要について説明します。 この記事では、拡張機能のインストール、状態、および削除の手順についても説明します。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

この記事のクラシック バージョンを確認するには、[SQL Server VM 用 SQL Server IaaS Agent 拡張機能 (クラシック)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md) に関する記事をご覧ください。

SQL Server IaaS 拡張機能には 3 つの管理モードがあります。 

- **フル** モードでは、すべての機能が提供されますが、SQL Server の再起動とシステム管理者権限が必要です。 これは、既定でインストールされるオプションです。 単一のインスタンスがある SQL Server VM を管理する場合に使用します。 

- **軽量**では SQL Server の再起動は必要ありませんが、サポートされるのはライセンスの種類と SQL Server のエディションの変更のみです。 このオプションは、複数のインスタンスを備えているかフェールオーバー クラスター インスタンス (FCI) に参加している SQL Server VM で使用します。 

- **NoAgent** は Windows Server 2008 にインストールされた SQL Server 2008 および SQL Server 2008 R2 の専用モードです。 Windows Server 2008 イメージに対するこのモードの使用については、[Windows Server 2008 の登録](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms)に関する記事を参照してください。 

## <a name="supported-services"></a>サポートされているサービス
SQL Server IaaS Agent 拡張機能は、次の管理タスクをサポートします。

| 管理機能 | 説明 |
| --- | --- |
| **SQL Server の自動バックアップ** |VM 上の SQL Server の既定のインスタンスまたは[適切にインストールされた](virtual-machines-windows-sql-server-iaas-faq.md#administration)名前付きインスタンスについて、すべてのデータベースのバックアップを自動的にスケジュールします。 詳細については、[Azure 仮想マシンでの SQL Server の自動バックアップ (Resource Manager)](virtual-machines-windows-sql-automated-backup.md) に関する記事を参照してください。 |
| **SQL Server の自動修正** |VM で重要な更新プログラムを実行できるメンテナンス期間を構成します。これにより、ワークロードのピーク時の更新を回避できます。 詳細については、「[Azure Virtual Machines での SQL Server の自動修正 (Resource Manager)](virtual-machines-windows-sql-automated-patching.md)」をご覧ください。 |
| **Azure Key Vault の統合** |SQL Server VM に Azure Key Vault を自動的にインストールして構成できます。 詳細については、「[Azure Virtual Machines 上の SQL Server 向け Azure Key Vault 統合の構成 (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md)」をご覧ください。 |

SQL Server Iaas Agent 拡張機能をインストールして実行すると、管理機能が使用できるようになります。

* Azure portal の仮想マシンの [SQL Server] パネル上と、Azure Marketplace の SQL Server イメージ用の Azure PowerShell を通して。
* 拡張機能を手動でインストールするための Azure PowerShell を通して。 

## <a name="prerequisites"></a>前提条件
VM で SQL Server IaaS Agent 拡張機能を使用するための要件を次に示します。

**[オペレーティング システム]** :

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server のバージョン**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [最新の Azure PowerShell コマンドをダウンロードして構成します](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>管理モードを変更する

PowerShell を使用して、次のように SQL Server IaaS エージェントの現在のモードを確認できます。 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

"*軽量*" の IaaS 拡張機能がインストールされている SQL Server VM の場合、Azure portal を使用してモードを "_フル_" にアップグレードできます。 _No-Agent_ モードの SQL Server VM は、OS が Windows 2008 R2 以降にアップグレードされ後で "_フル_" にアップグレードできます。 ダウングレードはできません。これを行うには、SQL IaaS 拡張機能を完全にアンインストールし、再度インストールする必要があります。 

エージェントのモードを "フル" にアップグレードするには: 


# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. [SQL 仮想マシン](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) リソースに移動します。 
1. 対象の SQL Server 仮想マシンを選択し、 **[概要]** を選択します。 
1. IaaS モードが NoAgent または軽量である SQL Server VM では、**SQL IaaS 拡張機能で利用できるのはライセンスの種類とエディションの更新のみ**という内容のメッセージを選択します。

   ![ポータルでモードを変更するための選択](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. **[仮想マシンで SQL Server サービスを再起動することに同意します]** チェック ボックスをオンにし、 **[確認]** を選択して IaaS モードを "フル" にアップグレードします。 

    ![仮想マシンで SQL Server サービスを再起動することに同意するためのチェック ボックス](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

次の Az CLI コード スニペットを実行します。

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

次の PowerShell コード スニペットを実行します。

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```

---


##  <a name="installation"></a>インストール
SQL Server IaaS 拡張機能は、SQL Server VM を [SQL VM リソース プロバイダー](virtual-machines-windows-sql-register-with-resource-provider.md)に登録するとインストールされます。 必要に応じて、フル モードまたは軽量モードを使用して SQL Server IaaS エージェントを手動でインストールできます。 

フル モードの SQL Server IaaS Agent 拡張機能は、Azure portal を使用して SQL Server 仮想マシンの Azure Marketplace イメージのいずれかをプロビジョニングすると自動的にインストールされます。 

### <a name="install-in-full-mode"></a>フル モードでインストールする
フル モードの SQL Server IaaS 拡張機能では、SQL Server VM 上の単一インスタンスを完全に管理できます。 既定のインスタンスがある場合、拡張機能は既定のインスタンスで動作し、その他のインスタンスの管理はサポートしません。 既定のインスタンスはないが、名前付きインスタンスが 1 つだけある場合、名前付きインスタンスが管理されます。 既定のインスタンスがなく、複数の名前付きインスタンスがある場合、拡張機能のインストールは失敗します。 

PowerShell を使用して、SQL Server IaaS エージェントをフル モードでインストールします。

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Install 'Full' SQL Server IaaS agent extension
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| パラメーター | 使用可能な値                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` または `PAYG`     |
| &nbsp;             | &nbsp;                          |


> [!NOTE]
> 拡張機能がまだインストールされていない場合、拡張機能をフル モードでインストールすると、SQL Server サービスが再起動します。 SQL Server サービスが再起動されないようにするには、管理性の制限がある軽量モードを代わりにインストールします。
> 
> SQL Server IaaS 拡張機能を更新しても、SQL Server サービスは再起動しません。 

### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>単一の名前付き SQL Server インスタンスを備えた VM にインストールする
SQL Server IaaS 拡張機能は、既定のインスタンスがアンインストールされて IaaS 拡張機能が再インストールされた場合に、SQL Server 上の名前付きインスタンスと連携します。

SQL Server の名前付きインスタンスを使用するには:
   1. Azure Marketplace から SQL Server VM をデプロイします。 
   1. [Azure portal](https://portal.azure.com) から、IaaS 拡張機能をアンインストールします。
   1. SQL Server VM 内で SQL Server を完全にアンインストールします。
   1. SQL Server VM 内の名前付きインスタンスで SQL Server をインストールします。 
   1. Azure portal から、IaaS 拡張機能をインストールします。  


### <a name="install-in-lightweight-mode"></a>軽量モードでインストールする
軽量モードでは、SQL Server サービスが再起動されませんが、提供される機能は限定されます。 

PowerShell を使用して、SQL Server IaaS エージェントを軽量モードでインストールします。


  ```powershell-interactive
     /#Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with the 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| パラメーター | 使用可能な値                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` または `PAYG`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>SQL Server IaaS 拡張機能の状態を取得する
拡張機能がインストールされていることを確認する 1 つの方法は、Azure ポータルにエージェントの状態を表示することです。 仮想マシンのウィンドウで **[すべての設定]** を選択し、 **[拡張機能]** を選択します。 **SqlIaasExtension** 拡張機能が一覧表示されます。

![Azure portal での SQL Server IaaS Agent 拡張機能の状態](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

**Get-AzVMSqlServerExtension** Azure PowerShell コマンドレットを使用することもできます。

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

前のコマンドでは、エージェントがインストールされていることが確認され、全般的な状態情報が提供されます。 次のコマンドを使用して、自動バックアップと自動修正に関する特定の状態情報を取得できます。

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>削除
Azure portal で、仮想マシンのプロパティの **[拡張機能]** ウィンドウにある省略記号を選択することで、拡張機能をアンインストールできます。 次に、 **[削除]** を選択します。

![Azure portal での SQL Server IaaS Agent 拡張機能のアンインストール](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

**Remove-AzVMSqlServerExtension** PowerShell コマンドレットを使用することもできます。

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>次の手順
拡張機能によってサポートされるいずれかのサービスの使用を開始します。 詳細については、この記事の「[サポートされているサービス](#supported-services)」セクションで参照されているトピックをご覧ください。

Azure Virtual Machines で SQL Server を実行する方法の詳細については、「[Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

