---
title: SQL IaaS Agent 拡張機能に登録する (Linux)
description: Linux Azure VM 上の SQL Server を SQL IaaS Agent 拡張機能に登録することにより、Azure の機能を有効にして、コンプライアンスと管理の容易性を向上させる方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: adbadram
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-Linux-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 2cf9219f5afc6805c147243ec0157280c839dc87
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092638"
---
# <a name="register-linux-sql-server-vm-with-sql-iaas-agent-extension"></a>Linux SQL Server VM を SQL IaaS Agent 拡張機能に登録する 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-agent-extension-manually-register-single-vm.md)
> * [Linux](sql-iaas-agent-extension-register-vm-linux.md)


お使いの SQL Server VM を [SQL IaaS Agent 拡張機能](sql-server-iaas-agent-extension-linux.md)に登録すると、Linux Azure VM 上の SQL Server で数多くの機能面の利点を活用できるようになります。

## <a name="overview"></a>概要

[SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-linux.md)に登録すると、仮想マシン リソースとは "_別の_" リソースである、**SQL 仮想マシン** "_リソース_" がサブスクリプション内に作成されます。 拡張機能から SQL Server VM を登録解除すると、**SQL 仮想マシン** の "_リソース_" は削除されますが、実際の仮想マシンは削除されません。

SQL IaaS Agent 拡張機能を利用するには、最初に [サブスクリプションを **Microsoft.SqlVirtualMachine** プロバイダーに登録する](#register-subscription-with-rp)必要があります。これにより、SQL IaaS 拡張機能は、その特定のサブスクリプション内にリソースを作成できます。

> [!IMPORTANT]
> SQL IaaS Agent 拡張機能を使用すると、Azure 仮想マシン内で SQL Server を使用する際に、お客様に追加のメリットを提供するという明確な目的のためにデータが収集されます。 Microsoft は、お客様の事前の同意なく、ライセンスの監査にこのデータを使用することはありません。 詳細については、「[SQL Server のプライバシーの補足情報](/sql/sql-server/sql-server-privacy#non-personal-data)」を参照してください。

## <a name="prerequisites"></a>前提条件

SQL Server VM を拡張機能に登録するには、以下のものが必要になります。

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- パブリック クラウドまたは Azure Government クラウドにデプロイされた Azure Resource Model の [Ubuntu Linux 仮想マシン](../../../virtual-machines/linux/quick-create-portal.md)と [SQL Server 2017 (またはそれ以降)](https://www.microsoft.com/sql-server/sql-server-downloads)。
- [Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell (最低 5.0)](/powershell/azure/install-az-ps) の最新バージョン。

## <a name="register-subscription-with-rp"></a>リソースプロバイダーを使用してサブスクリプションを登録する

SQL Server VM を SQL IaaS Agent 拡張機能に登録するには、最初にサブスクリプションを **Microsoft.SqlVirtualMachine** リソース プロバイダー (RP) に登録する必要があります。 これにより、SQL IaaS Agent 拡張機能は、サブスクリプション内にリソースを作成できます。 これは Azure portal、Azure CLI、または Azure PowerShell を使用して実行できます。

### <a name="azure-portal"></a>Azure portal

Azure portal を使用して、サブスクリプションをリソース プロバイダーに登録します。

1. Azure portal を開き、 **[すべてのサービス]** に移動します。
1. **[サブスクリプション]** に移動し、目的のサブスクリプションを選択します。
1. **[サブスクリプション]** ページで、 **[設定]** の **[リソース プロバイダー]** を選択します。
1. フィルター内に「**sql**」と入力し、SQL 関連のリソースプロバイダーを表示します。
1. 必要なアクションに応じて、**Microsoft.SqlVirtualMachine** プロバイダーで **[登録]** 、 **[再登録]** 、または **[登録解除]** を選択します。


![プロバイダーの変更](../windows/media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)

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

## <a name="register-vm"></a>VM の登録

Linux 上の SQL IaaS Agent 拡張機能は、SQL Server のライセンスの種類とエディションの変更のみがサポートされる軽量モードでのみ使用できます。 機能が制限される軽量モードで SQL Server VM を拡張機能に登録するには、Azure CLI または Azure PowerShell を使用します。 

SQL Server のライセンスの種類として、使用した分を支払う従量課金制 (`PAYG`)、独自のライセンスを使用する Azure ハイブリッド特典 (`AHUB`)、または [無料の DR レプリカ ライセンス](../windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)をアクティブ化するディザスター リカバリー (`DR`) のいずれかを指定します。

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

## <a name="verify-registration-status"></a>登録状態を確認する

お使いの SQL Server VM が既に SQL IaaS Agent 拡張機能に登録されているかどうかは、Azure portal、Azure CLI、または Azure PowerShell を使用して確認できます。


### <a name="azure-portal"></a>Azure portal

Azure portal を使用して登録ステータスを確認します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. SQL 仮想マシン リソースに移動します。
1. 一覧から SQL Server VM を選択します。 お使いの SQL Server VM がここに表示されていない場合は、SQL IaaS Agent 拡張機能に登録されていない可能性があります。

### <a name="command-line"></a>コマンド ライン

Azure CLI または Azure PowerShell を使用して現在の SQL Server VM の登録状態を確認します。 登録が成功していれば、`ProvisioningState` に `Succeeded` と表示されます。

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI を使用して登録ステータスを確認します。

```azurecli-interactive
az sql vm show -n <vm_name> -g <resource_group>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell を使用して登録ステータスを確認します。

```powershell-interactive
Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
```

---

エラーは、SQL Server VM が拡張機能に登録されていないことを示します。


## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。

* [Windows VM における SQL Server の概要](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](frequently-asked-questions-faq.yml)
* [Windows VM 上の SQL Server の価格ガイダンス](../windows/pricing-guidance.md)
* [Windows VM 上の SQL Server のリリース ノート](../windows/doc-changes-updates-release-notes.md)
