---
title: Azure での SQL Server VM のライセンス モデルを変更する方法
description: Azure ハイブリッド特典を使用して、Azure の SQL 仮想マシンのライセンスを "従量課金制" から "ライセンス持ち込み" に切り替える方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 78ad784a45d2b0063932791daedc9b1ec1aafd72
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786761"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Azure での SQL Server 仮想マシンのライセンス モデルを変更する方法
このアーティクルでは、新しい SQL VM リソース プロバイダーである、**Microsoft.SqlVirtualMachine** を使用して Azure での SQL Server 仮想マシンのライセンス モデルを変更する方法を説明します。

SQL Server をホストする仮想マシン (VM) には、従量課金制と Azure ハイブリッド特典 (AHB) の 2 種類のライセンス モデルがあります。 そして現在は、Azure portal、Azure CLI、または PowerShell を使用して、SQL Server VM のライセンス モデルを変更することができます。 

**従量課金制** (PAYG) モデルでは、Azure VM を実行する秒単位のコストに SQL Server ライセンスのコストが含まれます。
[Azure ハイブリッド特典 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) では、SQL Server を実行する VM に伴うお客様ご自身の SQL Server ライセンスを使用することができます。 

SQL Server 向け Microsoft Azure ハイブリッド特典では、SQL Server ライセンスを、Azure Virtual Machines 上のソフトウェア アシュアランス ("条件を満たしたライセンス") 付きで使用できます。 SQL Server 向け Azure ハイブリッド特典では、お客様は VM での SQL Server ライセンスの使用に対して課金されることはありませんが、対象のクラウド コンピューティング (つまり基本料金)、ストレージ、バックアップ、およびサービスの使用に関連付けられた I/O (該当する場合) のコストに対して料金を支払う必要があります。

Microsoft の製品条項に従って、"お客様は、Azure にワークロードを構成する際には、SQL Server 向け Azure ハイブリッド特典に基づいて Azure SQL Database (Managed Instance、Elastic Pool、Single Database)、Azure Data Factory、SQL Server Integration Services、または SQL Server Virtual Machines を使用しているということを示す必要があります。"

Azure VM 上の SQL Server 向け Azure ハイブリッド特典を使用していることと、準拠していることを示すには、次の 3 つのオプションがあります。

1. Azure マーケットプレースの BYOL SQL Server イメージを使用して仮想マシンをプロビジョニングします (エンタープライズ契約のお客様のみ利用可能)。
1. Azure マーケットプレースの PAYG SQL Server イメージを使用して仮想マシンをプロビジョニングし、AHB を有効化します。
1. SQL Server を Azure VM にセルフインストールし、手動で [SQL Server VM を登録](virtual-machines-windows-sql-register-with-resource-provider.md)し、AHB を有効化します。

SQL Server のライセンスの種類は、VM がプロビジョニングされるときに設定され、後でいつでも変更できます。 ライセンス モデル間の切り替えにより**ダウンタイムは発生せず**、VM は再起動されず、**追加コストは発生せず** (実際には、AHB の有効化によりコストが*削減されます*)、**ただちに有効となります**。 

## <a name="prerequisites"></a>前提条件

SQL VM リソース プロバイダーを利用するには、SQL IaaS 拡張機能が必要です。 そのため、SQL VM リソース プロバイダーの利用を続けるには、以下が必要です。
- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- [ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)。 
- [SQL VM リソース プロバイダー](virtual-machines-windows-sql-register-with-resource-provider.md)に登録された [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) がインストール済み。 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>リソース プロバイダーに既に登録されている VM のライセンスを変更する 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

ライセンス モデルをポータルから直接変更できます。 

1. [Azure portal](https://portal.azure.com) を開き、お使いの SQL Server VM 用の [SQL 仮想マシン リソース](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource)を起動します。 
1. **[設定]** の **[構成]** を選択します。 
1. **[Azure ハイブリッド特典]** オプションを選択し、ソフトウェア アシュアランス付きの SQL Server ライセンスがあることを確認するチェックボックスをオンにします。 
1. **[構成]** ページの下部にある **[適用]** を選択します。 

![ポータルの AHB](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Azure CLI を使用して、ライセンス モデルを変更することができます。  

次のコード スニペットでは、従量課金制ライセンス モデルを BYOL (Azure ハイブリッド特典の使用) に切り替えます。

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

次のコード スニペットは、BYOL モデルを従量課金制に切り替えます。 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
PowerShell を使用してライセンス モデルを変更できます。

次のコード スニペットでは、従量課金制ライセンス モデルを BYOL (Azure ハイブリッド特典の使用) に切り替えます。

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

次のコード スニペットでは、BYOL モデルを従量課金制に切り替えます。

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```
---

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>リソース プロバイダーに登録されていない VM のライセンスを変更する

PAYG Azure Marketplace イメージから SQL Server VM をプロビジョニングした場合、SQL ライセンスの種類は PAYG になります。 Azure Marketplace の BYOL イメージを使用して SQL Server VM をプロビジョニングした場合、ライセンスの種類は AHUB になります。 既定 (PAYG) または BYOL Azure Marketplace イメージからプロビジョニングされたすべての SQL Server VM は、[ライセンスの種類](#change-license-for-vms-already-registered-with-resource-provider)を変更できるように、SQL VM リソース プロバイダーに自動的に登録されます。

Azure ハイブリッド特典を通じた Azure VM 上の SQL Server のセルフインストールのみを行え、SQL Server license を AHB として設定して[これらの VM を SQL VM リソース プロバイダーに登録](virtual-machines-windows-sql-register-with-resource-provider.md)し、Microsoft 製品条項に従って AHB を使用することを示す必要があります。

SQL VM を SQL VM リソース プロバイダーに登録した場合、SQL Server VM のリソースの種類を PAYG または AHB にのみ変更でき、ライセンスのコンプライアンスのためにすべての SQL VM を SQL VM RP に登録する必要があります。

## <a name="remarks"></a>解説

 - Azure Cloud Solution Partner (CSP) のお客様は、アクティブな SA がある場合、最初に従量課金制の VM をデプロイした後、それをライセンス持ち込みに変換することによって、Azure ハイブリッド特典を利用できます。
 - SQL Server VM リソースを削除する場合は、イメージのハード コーディングされたライセンス設定に戻ります。 
  - ライセンス モデルを変更する機能は、SQL VM リソース プロバイダーの機能です。 Azure portal を介してマーケットプレース イメージをデプロイすると、SQL Server VM がリソース プロバイダーに自動的に登録されます。 ただし、SQL Server を自分でインストールするお客様は、手動で [SQL Server VM を登録](virtual-machines-windows-sql-register-with-resource-provider.md)する必要があります。 
- SQL Server VM を可用性セットに追加するには、VM を再作成する必要があります。 そのため、可用性セットに追加されたすべての VM は既定の従量課金制ライセンス タイプに戻ることになります。さらに、AHB をもう一度有効にする必要があります。 


## <a name="limitations"></a>制限事項

 - ライセンス モデルの変更は、ソフトウェア アシュアランスをお持ちのお客様のみご利用いただけます。
 - ライセンス モデルの変更は、SQL Server の Standard Edition および Enterprise Edition でのみサポートされています。 Express、Web、および Developer でのライセンスの変更はサポートされていません。 
 - ライセンス モデルの変更は、Resource Manager モデルを使用してデプロイされた仮想マシンについてのみサポートされます。 クラシック モデルを使用してデプロイされた VM はサポートされません。 
 - ライセンス モデルの変更は、パブリック クラウドのインストールでのみ有効です。
 - ライセンス モデルの変更は、1 つの NIC (ネットワーク インターフェイス) を持つ仮想マシンでのみサポートされます。 複数の NIC を持つ仮想マシンでは、手順を実行する前に、まず (Azure portal を使用して) NIC の 1 つを削除する必要があります。 そうしないと、次のようなエラーが発生します。`The virtual machine '\<vmname\>' has more than one NIC associated.` ライセンス モードを変更した後に NIC を再び VM に追加できる可能性はありますが、Azure portal の SQL 構成ページを介して実行された操作は、自動パッチや自動バックアップと同様に、もうサポートされていると見なされなくなります。


## <a name="known-errors"></a>既知のエラー

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>リソース 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' が見つかりませんでした。 このオブジェクトにプロパティ 'sqlServerLicenseType' が見つかりません。 プロパティが存在し、設定可能であることを確認してください。
このエラーは、SQL Server VM で SQL VM リソース プロバイダーに登録されていないライセンス モデルを変更しようとしたときに発生します。 リソース プロバイダーを[サブスクリプション](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription)に登録した後、SQL Server VM を SQL [リソース プロバイダー](virtual-machines-windows-sql-register-with-resource-provider.md)に登録する必要があります。 

### <a name="cannot-validate-argument-on-parameter-sku"></a>パラメーター 'Sku' の引数を検証できない
4\.0 より後の Azure PowerShell を使用している場合に、SQL Server VM のライセンス モデルを変更しようとすると、次のエラーが発生する可能性があります。`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

このエラーを解決するには、ライセンス モデルを切り替えるときに、前に説明した PowerShell コード スニペットの以下の行のコメントを解除します。

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
Azure PowerShell のバージョンを確認するには、次のコードを使用します。
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM における SQL Server に関するよくあるご質問](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM における SQL Server に関する料金ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM における SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)


