---
title: Azure の SQL Server 仮想マシンを SQL VM リソースプロバイダーに登録する | Microsoft Docs
description: SQL Server VM を SQL VM リソースプロバイダーに登録して管理性を向上させます。
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
ms.openlocfilehash: 95a167cbc8fde4488e3f46ffd850f0619cf1b651
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305869"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Azure の SQL Server 仮想マシンを SQL VM リソースプロバイダーに登録する

この記事では、Azure SQL Server 仮想マシン (VM) を SQL VM リソースプロバイダーに登録する方法について説明します。 

Azure portal を介して SQL Server VM マーケットプレース イメージをデプロイすると、自動的に SQL Server VM がリソースプロバイダーに登録されます。 ただし、Azure Marketplace からイメージを選択するのではなく、SQL Server を Azure 仮想マシンにセルフインストールすることを選択した場合は、以下の目的で、SQL Server VM をすぐにリソースプロバイダーに登録する必要があります。

-  **コンプライアンス** - Microsoft 製品の利用規約に従って、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を利用されるお客様は、いつ Azure ハイブリッド特典を利用するかを Microsoft に知らせる必要があり、そのために SQL VM リソースプロバイダーへの登録を行わなければなりません。 

-  **機能面の利点** - SQL Server VM をリソースプロバイダーに登録すると、[自動修正](virtual-machines-windows-sql-automated-patching.md)、[自動バックアップ](virtual-machines-windows-sql-automated-backup-v2.md)、監視および管理性機能が利用できるようになるほか、[ライセンス](virtual-machines-windows-sql-ahb.md)や[エディション](virtual-machines-windows-sql-change-edition.md)を柔軟に管理できるようになります。 これらの機能は、以前は Azure Marketplace の SQL Server VM イメージでしか利用できませんでした。

SQL Server を Azure VM にセルフインストールするか、SQL Server を備えたカスタム VHD から Azure VM をプロビジョニングすれば、SQL Server 向け Azure ハイブリッド特典を通じ、お客様がその利用を SQL VM リソースプロバイダーへの登録によって Microsoft に知らせるという条件を満たせます。 

SQL VM リソースプロバイダーを利用するには、SQL VM リソースプロバイダーをサブスクリプションに登録することも必要です。 これは、Azure portal、Azure CLI、および PowerShell で行えます。 

## <a name="prerequisites"></a>前提条件

SQL Server VM をリソースプロバイダーに登録するには、以下のものが必要になります。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 
- [Azure CLI](/cli/azure/install-azure-cli) と [PowerShell](/powershell/azure/new-azureps-module-az)。 

## <a name="register-with-sql-vm-resource-provider"></a>SQL VM リソースプロバイダーに登録する
[SQL IaaS 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)が既に VM にインストールされている場合、SQL VM リソースプロバイダーに登録すると、Microsoft.SqlVirtualMachine または SqlVirtualMachines 型のメタデータ リソースが作成されます。 以下は、SQL IaaS 拡張機能が既に VM にインストールされている場合に SQL VM リソースプロバイダーへの登録を行うコード スニペットです。 SQL VM リソースプロバイダーへの登録時に必要な SQL Server ライセンスの種類として "PAYG" または "AHUB" を指定する必要があります。 

PowerShell で次のコード スニペットを使用して、SQL Server VM を登録します。

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

SQL IaaS 拡張機能が VM にインストールされていない場合は、軽量 SQL 管理モードを指定することによって SQL VM リソースプロバイダーに登録できます。 軽量 SQL 管理モードでは、SQL VM リソースプロバイダーによって SQL IaaS 拡張機能が[軽量モード](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)で自動インストールされ、SQL Server インスタンス メタデータが検証されます。その際、SQL Server サービスの再起動は行われません。 SQL VM リソースプロバイダーへの登録時に必要な SQL Server ライセンスの種類として "PAYG" または "AHUB" を指定する必要があります。 

PowerShell で次のコード スニペットを使用して、SQL Server VM を軽量 SQL 管理モードで登録します。

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

[軽量モード](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)で SQL VM リソースプロバイダーに登録することにより、コンプライアンスが確保され、柔軟なライセンス管理と SQL Server エディションのインプレース更新が可能になります。 フェールオーバー クラスター インスタンスとマルチインスタンス デプロイは、軽量モードでのみ SQL VM リソースプロバイダーに登録できます。 Azure portal で見つかる手順に従って[フル モード](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation)にアップグレードすれば、いつでも SQL Server を再起動して包括的な管理性機能を有効にすることができます。 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Windows Server 2008 VM 上の SQL Server 2008 および 2008 R2 を登録する

Windows Server 2008 にインストールされている SQL Server 2008 および 2008 R2 は、[NoAgent](virtual-machines-windows-sql-server-agent-extension.md) モードで SQL VM リソースプロバイダーに登録できます。 このオプションにより、コンプライアンスが確保され、SQL Server VM を Azure portal の限られた機能で監視できるようになります。

次の表では、登録時に指定するパラメーターに使用可能な値を詳細に示しています。

| パラメーター | 使用可能な値                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'` または `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` または `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Windows Server 2008 インスタンス上の SQL Server 2008 または 2008 R2 を登録するには、次の Powershell コード スニペットを使用します。  

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>登録状態を確認する
SQL Server が既に SQL VM リソースプロバイダーに登録されているかどうかは、Azure portal、Azure CLI、または PowerShell を使用して確認できます。 

### <a name="azure-portal"></a>Azure ポータル 
Azure portal を使用して登録の状態を確認するには、以下を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. [SQL 仮想マシン](virtual-machines-windows-sql-manage-portal.md)に移動します。
1. 一覧から SQL Server VM を選択します。 SQL Server VM がここに表示されていない場合、その SQL Server VM は SQL VM リソースプロバイダーに登録されていない可能性があります。 
1. [`Status`] の値を確認します。 `Status = Succeeded` の場合は、SQL Server VM が SQL VM リソースプロバイダーに正常に登録されています。 

    ![SQL RP 登録による状態を確認する](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="az-cli"></a>AZ CLI

次の AZ CLI コマンドを使用して現在の SQL Server VM の登録状態を確認します。 登録が成功していれば、`ProvisioningState` に `Succeeded` と表示されます。 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


### <a name="powershell"></a>PowerShell

次の PowerShell コマンドレットを使用して現在の SQL Server VM の登録状態を確認します。 登録が成功していれば、`ProvisioningState` に `Succeeded` と表示されます。 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
エラーは、SQL Server VM がリソースプロバイダーに登録されていないことを示します。 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>SQL VM リソース プロバイダーをサブスクリプションに登録する 

自分の SQL Server VM を SQL VM リソースプロバイダーに登録するには、そのリソースプロバイダーを自分のサブスクリプションに登録する必要があります。 これは、Azure portal または Azure CLI を使用して行うことができます。

### <a name="azure-portal"></a>Azure ポータル

以下の手順では、Azure portal を使用して SQL VM リソースプロバイダーを Azure サブスクリプションに登録します。 

1. Azure portal を開き、**すべてのサービス**に移動します。 
1. **サブスクリプション**に移動し、関心のあるサブスクリプションを選択します。  
1. **[サブスクリプション]** ページで、 **[リソースプロバイダー]** に移動します。 
1. フィルター内に`sql`と入力し、SQL 関連のリソース プロバイダーを表示します。 
1. 目的となるアクションに応じて、*登録*、*再登録*、または*登録解除*のいずれかを **Microsoft.SqlVirtualMachine** プロバイダーで選択します。 

   ![プロバイダーの変更](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="az-cli"></a>AZ CLI
次のコード スニペットは、SQL VM リソース プロバイダーを Azure サブスクリプションに登録します。 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

次の PowerShell コード スニペットでは、SQL VM リソースプロバイダーを Azure サブスクリプションに登録します。

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>解説

 - SQL VM リソースプロバイダーでは、"Resource Manager" を使用してデプロイされた SQL Server VM のみがサポートされます。 "クラシック モデル" を使用してデプロイされた SQL Server VM はサポートされません。 
 - SQL VM リソースプロバイダーでは、パブリック クラウドにデプロイされた SQL Server VM のみがサポートされます。 プライベート クラウドや政府機関向けクラウドにデプロイされている場合はサポートされません。 
 
## <a name="frequently-asked-questions"></a>よく寄せられる質問 

**Azure Marketplace の SQL イメージからプロビジョニングされた SQL Server VM を登録する必要がありますか?**

いいえ。 Azure Marketplace の SQL イメージからプロビジョニングされた VM は自動登録されます。 SQL VM リソース プロバイダーに登録する必要があるのは、VM が Azure Marketplace の SQL イメージからプロビジョニングされておらず、SQL Server を自分でインストールした場合のみです。

**SQL VM リソース プロバイダーは、すべての顧客が使用できますか。** 

はい。 お客様が Azure Marketplace の SQL Server イメージを使用せずに SQL Server を自分でインストールしたか、カスタム VHD を導入した場合、SQL Server VM を SQL VM リソース プロバイダーに登録する必要があります。 すべての種類のサブスクリプション (Direct、EA、CSP) によって所有されている VM は、SQL VM リソース プロバイダーに登録できます。

**SQL Server VM に SQL IaaS 拡張機能が既にインストールされている場合、SQL VM リソース プロバイダーに登録する必要がありますか?**

SQL Server VM を、Azure Marketplace の SQL イメージからプロビジョニングせずに自分でインストールした場合、SQL IaaS 拡張機能をインストール済みであっても SQL VM リソース プロバイダーに登録する必要があります。 SQL VM リソース プロバイダーに登録すると、Microsoft.SqlVirtualMachines という種類の新しいリソースが作成されます。 SQL IaaS 拡張機能をインストールしても、そのリソースは作成されません。

**SQL VM リソース プロバイダーに登録したときの既定の SQL 管理モードは何ですか?**

SQL VM RP に登録したときの既定の SQL 管理モードは_完全_です。 SQL VM リソース プロバイダーに登録したときに SQL 管理プロパティが設定されていない場合、このモードは完全管理として設定されます。 SQL IaaS 拡張機能を VM にインストールすることが、完全管理モードで SQL VM リソース プロバイダーに登録するための前提条件です。

**SQL VM リソース プロバイダーに登録するための前提条件は何ですか?**

軽量モードまたはエージェントなしモードで SQL VM リソース プロバイダーに登録するための前提条件はありません。 完全モードで SQL VM リソース プロバイダーに登録するための前提条件は、SQL IaaS 拡張機能が VM にインストールされていることです。

**SQL IaaS 拡張機能を VM にインストールしていない場合、SQL VM リソース プロバイダーに登録できますか?**

はい、SQL IaaS 拡張機能が VM にインストールされていない場合は、軽量管理モードで SQL VM リソース プロバイダーに登録できます。 軽量モードでは、SQL VM リソース プロバイダーはコンソール アプリを使用して、SQL インスタンスのバージョンとエディションを確認します。 コンソール アプリは VM 上で少なくとも 1 つの SQL インスタンスが実行されていることを確認すると、自分自身をシャットダウンします。 軽量モードで SQL VM リソース プロバイダーに登録しても SQL Server は再起動されず、VM 上にエージェントは作成されません。

**SQL VM リソース プロバイダーに登録すると、VM にエージェントがインストールされますか?**

いいえ。 SQL VM リソース プロバイダーに登録すると、新しいメタデータ リソースのみが作成され、VM にエージェントがインストールされません。 SQL IaaS 拡張機能は完全管理を有効にするためにのみ必要であるため、管理モードを軽量モードから完全モードにアップグレードすると SQL IaaS 拡張機能がインストールされ、SQL Server が再起動します。

**SQL VM リソース プロバイダーに登録すると、VM 上の SQL Server が再起動されますか?**

いいえ。 SQL VM リソース プロバイダーに登録すると、新しいメタデータ リソースのみが作成され、VM 上の SQL Server は再起動されません。 SQL Server の再起動は SQL IaaS 拡張機能をインストールする場合にのみ必要で、SQL IaaS 拡張機能は完全管理を有効にするために必要です。 管理モードを軽量モードから完全モードにアップグレードすると、SQL IaaS 拡張機能がインストールされ、SQL Server が再起動されます。

**SQL VM リソース プロバイダーに登録するときの軽量モードとエージェントなし管理モードの違いは何ですか?** 

エージェントなし管理モードは Windows Server 2008 の SQL Server 2008/R2 でのみ使用でき、これらのバージョンで使用できる唯一の管理モードです。 その他のすべてのバージョンの SQL Server で使用可能な 2 つの管理モードは軽量モードと完全モードです。 エージェントなしモードでは SQL Server のバージョンとエディションのプロパティをお客様が設定する必要があります。軽量モードでは VM がクエリされて SQL インスタンスのバージョンとエディションが検索されます。

**Azure CLI を使用して、SQL VM リソース プロバイダーに軽量モードまたはエージェントなしモードで登録できますか?**

いいえ。 SQL 管理モード プロパティは、SQL VM リソース プロバイダーに Azure PowerShell で登録する場合にのみ使用できます。 Azure CLI は SQL 管理プロパティの設定をサポートしておらず、常に既定のモードである完全管理モードで SQL VM リソース プロバイダーに登録します。

**SQL ライセンスの種類を指定せずに SQL VM リソース プロバイダーに登録できますか?**

いいえ。 SQL VM RP に登録する場合、SQL ライセンスの種類は省略可能なプロパティではありません。 AZ CLI と PowerShell の両方で、すべての管理モード (エージェントなし、軽量、および完全) で SQL VM リソース プロバイダーに登録するときに、SQL ライセンスの種類を PAYG または AHUB として設定する必要があります。

**SQL IaaS 拡張機能をエージェントなしモードから完全モードにアップグレードできますか?**

いいえ。 エージェントなしモードでは、SQL 管理モードを完全モードまたは軽量モードにアップグレードすることはできません。 これは Windows Server 2008 の技術的な制限です。

**SQL IaaS 拡張機能を軽量モードから完全モードにアップグレードできますか?**

はい。 SQL 管理モードを軽量モードから完全モードにアップグレードすることは、PowerShell または Azure portal でサポートされています。また、SQL Server を再起動する必要があります。

**SQL IaaS 拡張機能を完全モードからエージェントなしモードまたは軽量管理モードにダウングレードすることはできますか?**

いいえ。 SQL IaaS 拡張機能の管理モードのダウングレードはサポートされていません。 SQL 管理モードは完全モードから軽量モードまたはエージェントなしモードにダウングレードできません。また、軽量モードからエージェントなしモードにダウングレードできません。 管理モードを完全管理から変更するには、SQL IaaS 拡張機能を削除し、Micorsoft.SqlVirtualMachine リソースを削除し、SQL Server VM を SQL VM リソース プロバイダーに再登録します。

**Azure portal から SQL VM リソース プロバイダーに登録できますか?**

いいえ。 Azure portal では SQL VM リソース プロバイダーに登録できません。 完全管理モードでの SQL VM リソース プロバイダーへの登録は、Azure CLI または PowerShell でサポートされています。また、軽量モードまたはエージェントなしの管理モードでの SQL VM リソース プロバイダーへの登録は、Azure PowerShell API でのみサポートされています。

**SQL Server をインストールする前に、VM を SQL VM リソース プロバイダーに登録できますか?**

いいえ。 SQL VM リソース プロバイダーに正常に登録するには、VM に少なくとも 1 つの SQL インスタンスが必要です。 VM 上に SQL インスタンスが存在しない場合、新しい Micosoft.SqlVirtualMachine リソースはエラー状態になります。

**複数の SQL インスタンスがある場合に、VM を SQL VM リソースに登録できますか?**

はい。 SQL VM リソース プロバイダーは SQL インスタンスを 1 つのみ登録します。 複数のインスタンスがある場合、SQL VM リソース プロバイダーは既定の SQL インスタンスを登録します。 既定のインスタンスがない場合、軽量モードでの登録のみがサポートされます。 軽量モードから完全管理モードにアップグレードするには、既定の SQL インスタンスが存在するか、VM に名前付き SQL インスタンスが 1 つのみ存在している必要があります。

**SQL Server フェールオーバー クラスター インスタンスを SQL VM リソース プロバイダーに登録できますか?**

はい。 Azure VM 上の SQL FCI インスタンスは SQL VM リソース プロバイダーに軽量モードで登録できます。 ただし、SQL FCI インスタンスを完全管理モードにアップグレードすることはできません。

**Always ON 可用性グループが構成されている場合、VM を SQL VM RP に登録できますか?**

はい。 Always ON 可用性グループ構成に参加している場合、Azure VM 上の SQL Server インスタンスを SQL VM リソース プロバイダーへ登録することに制限はありません。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM における SQL Server に関するよくあるご質問](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM における SQL Server に関する料金ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM における SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)
