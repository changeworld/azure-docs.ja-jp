---
title: Azure の SQL Server 仮想マシンを SQL VM リソース プロバイダーに登録する | Microsoft Docs
description: SQL Server VM を SQL VM リソース プロバイダーに登録して管理性を向上させます。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c78b5d71fffbf579b15f747c048bd65259039749
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786743"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Azure の SQL Server 仮想マシンを SQL VM リソース プロバイダーに登録する

この記事では、Azure SQL Server 仮想マシン (VM) を SQL VM リソース プロバイダーに登録する方法について説明します。 

Azure portal を介して SQL Server VM マーケットプレース イメージをデプロイすると、SQL Server VM がリソース プロバイダーに自動的に登録されます。 ただし、Azure Marketplace からイメージを選択するのではなく、SQL Server を Azure 仮想マシンにセルフインストールするという場合は、以下のような目的で、SQL Server VM をすぐにリソース プロバイダーに登録する必要があります。

-  **コンプライアンス** - Microsoft 製品使用条件に定められているとおり、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を利用されるお客様は、いつ Azure ハイブリッド特典を利用するのかを Microsoft に知らせる必要があり、そのために SQL VM リソース プロバイダーへの登録を行わなければなりません。 

-  **機能面の利点** - SQL Server VM をリソース プロバイダーに登録すると、[自動修正](virtual-machines-windows-sql-automated-patching.md)、[自動バックアップ](virtual-machines-windows-sql-automated-backup-v2.md)、監視および管理性機能が利用できるようになるほか、[ライセンス](virtual-machines-windows-sql-ahb.md)や[エディション](virtual-machines-windows-sql-change-edition.md)を柔軟に管理できるようになります。 これらの機能は、以前は Azure Marketplace の SQL Server VM イメージでしか利用できませんでした。

SQL Server を Azure VM にセルフインストールするか、SQL Server を備えたカスタム VHD から Azure VM をプロビジョニングすれば、SQL Server 向け Azure ハイブリッド特典を通じ、お客様がその利用を SQL VM リソース プロバイダーへの登録によって Microsoft に知らせるという条件を満たせます。 

SQL VM リソース プロバイダーを利用するには、SQL VM リソース プロバイダーをサブスクリプションに登録することも必要です。 この登録は、Azure portal、Azure CLI、および PowerShell で行えます。 

## <a name="prerequisites"></a>前提条件

SQL Server VM をリソース プロバイダーに登録するには、以下のものが必要になります。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 
- [Azure CLI](/cli/azure/install-azure-cli) と [PowerShell](/powershell/azure/new-azureps-module-az)。 

## <a name="register-with-sql-vm-resource-provider"></a>SQL VM リソース プロバイダーに登録する
[SQL IaaS 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)が既に VM にインストールされている場合は、SQL VM リソース プロバイダーに登録すると、Microsoft.SqlVirtualMachine または SqlVirtualMachines 型のメタデータ リソースが作成されます。 以下は、SQL IaaS 拡張機能が既に VM にインストールされている場合に SQL VM リソース プロバイダーへの登録を行うコード スニペットです。 SQL VM リソース プロバイダーへの登録時に、必要な SQL Server ライセンスの種類として "PAYG" または "AHUB" を指定する必要があります。 

PowerShell で次のコード スニペットを使用して、SQL Server VM を登録します。

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'}  
  
  ```

SQL IaaS 拡張機能が VM にインストールされていない場合は、軽量 SQL 管理モードを指定することによって SQL VM リソース プロバイダーに登録できます。 軽量 SQL 管理モードでは、SQL VM リソース プロバイダーによって SQL IaaS 拡張機能が[軽量モード](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)で自動インストールされ、SQL Server インスタンス メタデータが検証されます。その際、SQL Server サービスの再起動は行われません。 SQL VM リソース プロバイダーへの登録時に、必要な SQL Server ライセンスの種類として "PAYG" または "AHUB" を指定する必要があります。 

PowerShell で次のコード スニペットを使用して、SQL Server VM を軽量 SQL 管理モードで登録します。

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```
SQL VM リソース プロバイダーを[軽量モード](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)で登録することにより、コンプライアンスが確保され、柔軟なライセンス管理と SQL Server エディションのインプレース更新が行えるようになります。 フェールオーバー クラスター インスタンスとマルチインスタンス デプロイは、軽量モードでのみ SQL VM リソース プロバイダーに登録できます。 Azure portal で見つかる手順に従って[フル モード](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation)にアップグレードすれば、いつでも SQL Server を再起動して包括的な管理性機能を有効にすることができます。 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Windows Server 2008 VM 上の SQL Server 2008 および 2008 R2 を登録する

Windows Server 2008 にインストールされた SQL Server 2008 および 2008 R2 は、[NoAgent](virtual-machines-windows-sql-server-agent-extension.md) モードで SQL VM リソース プロバイダーに登録できます。 このオプションにより、コンプライアンスが確保され、SQL Server VM を Azure portal の限られた機能で監視できるようになります。

次の表は、登録時に指定するパラメーターとして使用できる値の詳細です。

| パラメーター | 使用可能な値                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'` または `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` または `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Windows Server 2008 インスタンス上の SQL Server 2008 または 2008 R2 を登録するには、次の Powershell コード スニペットを使用します。  

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```



## <a name="verify-registration-status"></a>登録状態を確認する
SQL Server が既に SQL VM リソース プロバイダーに登録されているかどうかを、Azure portal、Azure CLI、または PowerShell を使用して確認できます。 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Azure portal を使用して登録の状態を確認するには、以下の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. [SQL 仮想マシン](virtual-machines-windows-sql-manage-portal.md)に移動します。
1. 一覧から SQL Server VM を選択します。 SQL Server VM が一覧にない場合、その SQL Server VM は SQL VM リソース プロバイダーに登録されていない可能性があります。 
1. [`Status`] の値を確認します。 `Status = Succeeded` の場合は、SQL Server VM が SQL VM リソース プロバイダーに正常に登録されています。 

    ![SQL RP 登録による状態を確認する](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

次の AZ CLI コマンドでの現在の SQL Server VM の登録状態を確認します。 登録が成功していれば、`ProvisioningState` に `Succeeded` と表示されます。 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

次の PowerShell コマンドレットで現在の SQL Server VM 登録状態を確認します。 登録が成功していれば、`ProvisioningState` に `Succeeded` と表示されます。 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
SQL Server VM がリソース プロバイダーに登録されていないことを示すエラー。 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>SQL VM リソース プロバイダーをサブスクリプションに登録する 

自分の SQL Server VM を SQL VM リソース プロバイダーに登録するには、リソース プロバイダーを自分のサブスクリプションに登録する必要があります。 これは、Azure portal または Azure CLI を使用して行うことができます。

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)


以下の手順では、Azure portal を使用して SQL VM リソース プロバイダーを Azure サブスクリプションに登録します。 

1. Azure portal を開き、**すべてのサービス**に移動します。 
1. **サブスクリプション**に移動し、関心のあるサブスクリプションを選択します。  
1. **[サブスクリプション]** ページで、 **[リソース プロバイダー]** に移動します。 
1. フィルター内に`sql`と入力し、SQL 関連のリソース プロバイダーを表示します。 
1. 目的となるアクションに応じて、*登録*、*再登録*、または*登録解除*のいずれかを **Microsoft.SqlVirtualMachine** プロバイダーで選択します。 

   ![プロバイダーの変更](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

次のコード スニペットは、SQL VM リソース プロバイダーを Azure サブスクリプションに登録します。 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

次の PowerShell コード スニペットでは、SQL VM リソース プロバイダーを Azure サブスクリプションに登録します。

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>解説

 - SQL VM リソース プロバイダーでは、"Resource Manager" を使用してデプロイされた SQL Server VM のみがサポートされます。 "クラシック モデル" を使用してデプロイされた SQL Server VM はサポートされません。 
 - SQL VM リソース プロバイダーでは、パブリック クラウドにデプロイされた SQL Server VM のみがサポートされます。 プライベート クラウドや政府機関向けクラウドにデプロイされている場合はサポートされません。 

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM における SQL Server に関するよくあるご質問](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM における SQL Server に関する料金ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM における SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)


