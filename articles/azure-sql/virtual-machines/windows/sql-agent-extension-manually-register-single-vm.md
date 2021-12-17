---
title: SQL IaaS 拡張機能に登録する
description: SQL IaaS Agent 拡張機能を使用して Windows azure VM に SQL Server を登録する方法について説明します。これにより、azure の機能が有効になり、コンプライアンスと管理の容易性も向上します。
services: virtual-machines-windows
documentationcenter: na
author: adbadram
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 20177246aa0b39dc7a7c254c4dfb964d0444b631
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044021"
---
# <a name="register-windows-sql-server-vm-with-sql-iaas-extension"></a>SQL IaaS 拡張機能を使用して Windows SQL Server VM を登録する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-agent-extension-manually-register-single-vm.md)
> * [Linux](../linux/sql-iaas-agent-extension-register-vm-linux.md)

SQL Server VM を[SQL IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に登録して、Windows Azure VM での SQL Server の豊富な機能の特典を解除します。 

この記事では、SQL IaaS Agent 拡張機能に 1 つの SQL Server VM を登録する方法について説明します。 または、スクリプトを使用して、サブスクリプション内のすべての SQL Server vm を[自動的に](sql-agent-extension-automatic-registration-all-vms.md)登録することも、[複数の vm を一括で](sql-agent-extension-manually-register-vms-bulk.md)登録することもできます。

> [!NOTE]
> 2021 年 9 月以降は、SQL IaaS 拡張機能をフル モードで登録しても、SQL Server サービスを再起動する必要はありません。 

## <a name="overview"></a>概要

[SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に登録すると、仮想マシン リソースとは "_別の_" リソースである、[**SQL 仮想マシン** "_リソース_"](manage-sql-vm-portal.md) がサブスクリプション内に作成されます。 拡張機能から SQL Server VM を登録解除すると、**SQL 仮想マシン** "_リソース_" は削除されますが、実際の仮想マシンは削除されません。

Azure portal を介して SQL Server VM の Azure Marketplace イメージをデプロイすると、その SQL Server VM が自動的に拡張機能に登録されます。 ただし、Azure 仮想マシンに SQL Server を自分でインストールすること、またはカスタム VHD から Azure 仮想マシンをプロビジョニングすることを選択する場合は、機能面のすべての利点と管理の容易性を活用できるようにするために、SQL Server VM を SQL IaaS Agent 拡張機能に登録する必要があります。

SQL IaaS Agent 拡張機能を利用するには、最初に [サブスクリプションを **Microsoft.SqlVirtualMachine** プロバイダーに登録する](#register-subscription-with-rp)必要があります。これにより、SQL IaaS 拡張機能は、その特定のサブスクリプション内にリソースを作成できます。 その後、SQL Server VM を拡張機能に登録できます。 

既定で、SQL Server 2016 以降がインストールされている Azure VM は、[CEIP サービス](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server)によって検出されると、SQL IaaS Agent 拡張機能に自動的に登録されます。  詳細については、「[SQL Server のプライバシーの補足情報](/sql/sql-server/sql-server-privacy#non-personal-data)」を参照してください。

> [!IMPORTANT]
> SQL IaaS Agent 拡張機能を使用すると、Azure 仮想マシン内で SQL Server を使用する際に、お客様に追加のメリットを提供するという明確な目的のためにデータが収集されます。 Microsoft は、お客様の事前の同意なく、ライセンスの監査にこのデータを使用することはありません。 詳細については、「[SQL Server のプライバシーの補足情報](/sql/sql-server/sql-server-privacy#non-personal-data)」を参照してください。

## <a name="prerequisites"></a>前提条件

SQL Server VM を拡張機能に登録するには、以下のものが必要になります。

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- パブリック クラウドまたは Azure Government クラウドにデプロイされた Azure Resource Model の [Windows Server 2008 (またはそれ以降) の仮想マシン](../../../virtual-machines/windows/quick-create-portal.md)と [SQL Server 2008 (またはそれ以降)](https://www.microsoft.com/sql-server/sql-server-downloads)。
- [Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell (最低 5.0)](/powershell/azure/install-az-ps) の最新バージョン。
- .NET Framework 4.5.1 以降。

## <a name="register-subscription-with-rp"></a>リソースプロバイダーを使用してサブスクリプションを登録する

SQL Server VM を SQL IaaS Agent 拡張機能に登録するには、最初にサブスクリプションを **Microsoft.SqlVirtualMachine** リソース プロバイダー (RP) に登録する必要があります。 これにより、SQL IaaS Agent 拡張機能は、サブスクリプション内にリソースを作成できます。 これは Azure portal、Azure CLI、または Azure PowerShell を使用して実行できます。

### <a name="azure-portal"></a>Azure portal

Azure portal を使用して、サブスクリプションをリソース プロバイダーに登録します。

1. Azure portal を開き、 **[すべてのサービス]** に移動します。
1. **[サブスクリプション]** に移動し、目的のサブスクリプションを選択します。
1. **[サブスクリプション]** ページで、 **[設定]** の **[リソース プロバイダー]** を選択します。
1. フィルター内に「**sql**」と入力し、SQL 関連のリソースプロバイダーを表示します。
1. 必要なアクションに応じて、**Microsoft.SqlVirtualMachine** プロバイダーで **[登録]** 、 **[再登録]** 、または **[登録解除]** を選択します。

   ![プロバイダーの変更](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)

### <a name="command-line"></a>コマンド ライン

Azure CLI または Azure PowerShell を使用して、Azure サブスクリプションを **Microsoft.SqlVirtualMachine** プロバイダーに登録します。

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI を使用して、サブスクリプションをリソース プロバイダーに登録します。

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell を使用して、サブスクリプションをリソース プロバイダーに登録します。

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="full-mode"></a>フル モード

Azure CLI と Azure PowerShell を使用して、SQL Server VM を完全モードで直接登録することも、Azure portal、Azure CLI、または Azure PowerShell を使用して簡易モードからフルモードにアップグレードすることもできます。 _noagent_ モードでの vm のアップグレードは、OS が Windows 2008 R2 以降にアップグレードされるまではサポートされていません。  

2021年9月以降、SQL Server VM をフルモードで登録するには、SQL Server サービスを再起動する必要がなくなりました。 

フル モードの詳細については、「[管理モード](sql-server-iaas-agent-extension-automate-management.md#management-modes)」を参照してください。

### <a name="register-in-full-mode"></a>フルモードで登録する

SQL Server のライセンスの種類として、使用した分を支払う従量課金制 (`PAYG`)、独自のライセンスを使用する Azure ハイブリッド特典 (`AHUB`)、または [無料の DR レプリカ ライセンス](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)をアクティブ化するディザスター リカバリー (`DR`) のいずれかを指定します。


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI を使用して、SQL Server VM をフルモードで登録します。

```azurecli-interactive
# Register Enterprise or Standard self-installed VM in Lightweight mode
az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> --sql-mgmt-type Full
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell を使用して、SQL Server VM をフルモードで登録します。

```powershell-interactive
# Get the existing Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
-LicenseType <license_type> -SqlManagementType Full
```

---


### <a name="upgrade-to-full"></a>フルへのアップグレード

"*軽量*" モードで拡張機能に登録された SQL Server VM は、Azure portal、Azure CLI、または Azure PowerShell を使用して "_フル_" にアップグレードできます。 "_No-Agent_" モードの SQL Server VM は、OS が Windows 2008 R2 以上にアップグレードされた後、"_フル_" にアップグレードできます。 ダウングレードすることはできません。それを行うには、SQL Server VM を SQL IaaS Agent 拡張機能から[登録解除](#unregister-from-extension)する必要があります。 それにより、**SQL 仮想マシン** の "_リソース_" が削除されますが、実際の仮想マシンは削除されません。

#### <a name="azure-portal"></a>Azure portal

Azure portal を使用して拡張機能をフルモードにアップグレードします。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. [SQL 仮想マシン](manage-sql-vm-portal.md#access-the-resource) リソースに移動します。
1. SQL Server VM を選択し、 **[概要]** ページに移動します。
1. IaaS 拡張機能モードが NoAgent または軽量である SQL Server VM では、**SQL IaaS 拡張機能の現在のモードで利用できるのはライセンスの種類とエディションの更新のみ** という内容のメッセージを選択します。

   ![ポータルでモードを変更するための選択](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. **[Confirm]\(確定\)** を選択して、SQL Server IaaS 拡張機能モードを "フル" にアップグレードします。

  ![**[Confirm]\(確定\)** を選択して、SQL Server IaaS 拡張機能モードを "フル" にアップグレードします。](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

#### <a name="command-line"></a>コマンド ライン

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI を使用して拡張機能をフルモードにアップグレードします。

```azurecli-interactive
# Update to full mode
az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell を使用して拡張機能をフルモードにアップグレードします。 

```powershell-interactive
# Get the existing  Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
# Register with SQL IaaS Agent extension in full mode
Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full -Location $vm.Location
```

---

## <a name="lightweight-mode"></a>軽量モード

機能が制限される軽量モードで SQL Server VM を拡張機能に登録するには、Azure CLI または Azure PowerShell を使用します。 

SQL Server のライセンスの種類として、使用した分を支払う従量課金制 (`PAYG`)、独自のライセンスを使用する Azure ハイブリッド特典 (`AHUB`)、または [無料の DR レプリカ ライセンス](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)をアクティブ化するディザスター リカバリー (`DR`) のいずれかを指定します。

複数のインスタンスを持つフェールオーバークラスターインスタンスと SQL Server vm は、簡易モードで SQL IaaS エージェント拡張機能にのみ登録できます。

軽量モードの詳細については、「[管理モード](sql-server-iaas-agent-extension-automate-management.md#management-modes)」を参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI を使用して軽量モードで SQL Server VM を登録します。

```azurecli-interactive
# Register Enterprise or Standard self-installed VM in Lightweight mode
az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell を使用して軽量モードで SQL Server VM を登録します。

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType <license_type>  -SqlManagementType LightWeight  
```

---

## <a name="noagent-mode"></a>NoAgent モード

Windows Server 2008 ("_R2 ではない_") にインストールされている SQL Server 2008 および 2008 R2 は、[NoAgent モード](sql-server-iaas-agent-extension-automate-management.md#management-modes)でのみ SQL IaaS Agent 拡張機能に登録できます。 このオプションにより、コンプライアンスが確保され、SQL Server VM を Azure portal の限られた機能で監視できるようになります。

**[ライセンスの種類]** には、`AHUB`、`PAYG`、または `DR` を指定します。 **[image offer]\(イメージ オファー\)** には、`SQL2008-WS2008` または `SQL2008R2-WS2008` を指定します。

Azure CLI または Azure PowerShell を使用して、SQL Server 2008 ( `SQL2008-WS2008` ) または 2008 R2 ( `SQL2008R2-WS2008` ) インスタンスを Windows Server 2008 VM に登録します。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI を使用して NoAgent モードで SQL Server 仮想マシンを登録します。

```azurecli-interactive
az sql vm create -n sqlvm -g myresourcegroup -l eastus |
--license-type <license type>  --sql-mgmt-type NoAgent 
--image-sku Enterprise --image-offer <image offer> 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell を使用して、NoAgent モードで SQL Server 仮想マシンを登録します。

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
```

---


## <a name="check-management-mode"></a>管理モード

Azure PowerShell を使用して、SQL Server IaaS エージェント拡張機能がどの管理モードにあるかを確認します。 

Azure PowerShell の拡張機能のモードを確認します。  

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```


## <a name="verify-registration-status"></a>登録状態を確認する

お使いの SQL Server VM が既に SQL IaaS Agent 拡張機能に登録されているかどうかは、Azure portal、Azure CLI、または Azure PowerShell を使用して確認できます。

### <a name="azure-portal"></a>Azure portal

次の手順で登録の状態Azure portal。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご利用の [SQL Server VM](manage-sql-vm-portal.md) にアクセスします。
1. 一覧から SQL Server VM を選択します。 お使いの SQL Server VM がここに表示されていない場合は、SQL IaaS Agent 拡張機能に登録されていない可能性があります。
1. **[状態]** の値を確認します。 **[状態]** が **[成功]** の場合は、その SQL Server VM が正常に SQL IaaS Agent 拡張機能に登録されています。

   ![SQL RP 登録による状態を確認する](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

または、 **[SQL 仮想マシン]** リソースの **[サポートとトラブルシューティング]** ウィンドウで **[修復]** を選択して状態を確認することもできます。 SQL IaaS エージェント拡張機能のプロビジョニング状態は、 **[成功]** または **[失敗]** になります。 

### <a name="command-line"></a>コマンド ライン

Azure CLI または Azure PowerShell を使用して現在の SQL Server VM の登録状態を確認します。 登録が成功していれば、`ProvisioningState` に `Succeeded` と表示されます。

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

次の手順で登録の状態を確認Azure CLI。 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

次のコマンドを使用して、登録Azure PowerShell。 

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

エラーは、SQL Server VM が拡張機能に登録されていないことを示します。

## <a name="repair-extension"></a>拡張機能の修復

SQL IaaS エージェント拡張機能が失敗状態になる可能性があります。 SQL IaaS エージェント拡張機能を修復するには、Azure portal を使用を使用します。 

次の方法で拡張機能を修復Azure portal。  

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご利用の [SQL Server VM](manage-sql-vm-portal.md) にアクセスします。
1. 一覧から SQL Server VM を選択します。 お使いの SQL Server VM がここに表示されていない場合は、SQL IaaS Agent 拡張機能に登録されていない可能性があります。
1. **[SQL 仮想マシン]** リソース ページの **[サポートとトラブルシューティング]** の下にある **[修復]** を選択します。 

   :::image type="content" source="media/sql-agent-extension-manually-register-single-vm/repair-extension.png" alt-text="[SQL 仮想マシン] リソース ページの [サポートとトラブルシューティング] の下にある [修復] を選択します":::   

1. プロビジョニングの状態が **[失敗]** と表示されている場合は、 **[修復]** を選択して拡張機能を修復します。 状態が **[成功]** の場合は、 **[強制修復]** の横にあるチェックボックスをオンにして、状態に関係なく拡張機能を修復できます。 

   ![プロビジョニングの状態が [失敗] と表示されている場合は、[修復] を選択して拡張機能を修復します。 状態が [成功] の場合は、[強制修復] の横にあるチェックボックスをオンにして、状態に関係なく拡張機能を修復できます。](./media/sql-agent-extension-manually-register-single-vm/force-repair-extension.png)


## <a name="unregister-from-extension"></a>拡張機能から登録を解除する

SQL IaaS Agent 拡張機能との SQL Server VM の登録を解除するには、Azure portal または Azure CLI を使用して、SQL 仮想マシン "*リソース*" を削除します。 SQL 仮想マシン "*リソース*" を削除しても、SQL Server VM は削除されません。 SQL IaaS Agent 拡張機能との SQL 仮想マシンの登録を解除するには、管理モードをフルからダウングレードする必要があります。

>[!CAUTION]
> **拡張機能から VM の** 登録を解除する場合SQL Server注意してください。 リソース を削除しようとするときに **仮想** マシンを誤って削除する可能性がある場合は、慎重に手順に従 *ってください*。


### <a name="azure-portal"></a>Azure portal

拡張機能から SQL Server VM の登録を解除するには、次のコマンドをAzure portal。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. SQL VM リソースに移動します。

   ![SQL 仮想マシン リソース](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. **[削除]** を選択します。

   ![上部のナビゲーションの [削除] を選択します](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. SQL 仮想マシンの名前を入力し、**仮想マシンの横にあるチェック ボックスをオフにします**。

   ![実際の仮想マシンが削除されないように仮想マシンのボックスをオフにし、[削除] を選択して SQL VM リソースの削除を続行します](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   > [!WARNING]
   > 仮想マシン名の横にあるチェックボックスをオフにしないと、仮想マシンが完全に "*削除*" されます。 拡張機能から SQL Server VM の登録を解除する一方で、"*実際の仮想マシンを削除しない*" 場合は、このチェックボックスをオフにしてください。

1. **[削除]** を選択して、SQL Server VM ではなく、SQL 仮想マシン "*リソース*" の削除を確認します。

### <a name="command-line"></a>コマンド ライン

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

拡張機能から SQL Server VM の登録を解除するには、Azure CLI [az sql vm delete コマンドを使用](/cli/azure/sql/vm#az_sql_vm_delete)します。 これにより、VM SQL Serverが *削除されますが*、仮想マシンは削除されます。

次のコマンドを使用SQL Server VM の登録を解除Azure CLI。 

```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

拡張機能から SQL Server VM の登録を解除するにはAzure PowerShell [Remove-AzSqlVM コマンドを使用](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm)します。 これにより、VM SQL Serverが *削除されますが*、仮想マシンは削除されます。

VM の登録をSQL Serverするには、次のAzure PowerShell。 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <SQL VM resource name>
```

---

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

* [Windows VM における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](frequently-asked-questions-faq.yml)
* [Azure VM でのSQL Serverの価格ガイダンス](../windows/pricing-guidance.md)
* [Azure VM 上の SQL Server の新機能](../windows/doc-changes-updates-release-notes-whats-new.md)
