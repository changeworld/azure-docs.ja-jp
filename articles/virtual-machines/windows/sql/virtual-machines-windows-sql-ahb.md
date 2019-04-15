---
title: Azure で SQL Server VM のライセンス モデルを変更する方法 |Microsoft Docs
description: Azure での SQL 仮想マシンのライセンスを切り替える方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
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
ms.openlocfilehash: bc3e2955049188b0794367d5391762f5eb50b1c0
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "58850194"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Azure での SQL Server 仮想マシンのライセンス モデルを変更する方法
このアーティクルでは、新しい SQL VM リソース プロバイダーである、**Microsoft.SqlVirtualMachine** を使用して Azure での SQL Server 仮想マシンのライセンス モデルを変更する方法を説明します。 SQL Server をホストする仮想マシン (VM) には、従量課金制とライセンス持ち込み (BYOL) の 2 種類のライセンス モデルがあります。 現在では、PowerShell または Azure CLI を使用して、SQL Server VM でどちらのライセンス モデルを使用するかを変更できます。 

**従量課金制** (PAYG) モデルでは、Azure VM を実行する秒単位のコストに SQL Server ライセンスのコストが含まれます。

**ライセンス持ち込み** (BYOL) モデルは [Azure ハイブリッド特典 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) とも呼ばれ、ユーザーは SQL Server を実行する VM で自分の SQL Server ライセンスを使用できます。 価格の詳細については、[SQL Server VM 料金ガイド](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)をご参照ください。

2 種類のライセンスモデル間の切り替えによる**ダウンタイム**および VM の再起動は、**追加コスト**は発生せず (実際には、AHB の有効化によるコスト*削減*)、**ただちに有効となります**。 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="remarks"></a>解説

 - 現在、ライセンス モデルの変換は、従量課金制の SQL Server VM イメージで始めた場合にのみ可能です。 ポータルからライセンス持ち込みイメージで始めた場合は、そのイメージを従量課金制に変換することはできません。
 - CSP のお客様は、最初に従量課金制の VM をデプロイした後、それをライセンス持ち込みに変換することによって、Azure ハイブリッド特典を利用できます。 
 - 現在、この機能はパブリック クラウド インストールでのみ有効です。
 - カスタム SQL Server VM イメージをリソース プロバイダーに登録するとき、ライセンスの種類を 'AHUB' として指定します。 ライセンスの種類を空白のままにするか、'PAYG' を指定すると、登録は失敗します。 

## <a name="prerequisites"></a>前提条件
SQL VM リソース プロバイダーを利用するには、SQL IaaS 拡張機能が必要です。 そのため、SQL VM リソース プロバイダーの利用を続けるには、以下が必要です。
- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- [ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)。 
- [SQL IaaS 拡張機能](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)がインストールされている*従量課金制*の [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 


## <a name="register-sql-resource-provider-to-your-subscription"></a>SQL リソース プロバイダーをサブスクリプションに登録する 

ライセンス モデル間の切り替えを可能とする能力は、新しい SQL VM リソース プロバイダー (Microsoft.SqlVirtualMachine) によって提供される機能です。 2018 年 12 月以降にデプロイされた SQL Server VM は、自動的に新しいリソース プロバイダーに登録します。 ただし、この日付より前にデプロイされた既存の VM は、リソース プロバイダーに手動で登録しないと、ライセンス モデルの切り替えが可能になりません。 

  > [!NOTE] 
  > SQL Server VM リソースを削除する場合は、イメージのハード コーディングされたライセンス設定に戻ります。 

自分の SQL Server VM を SQL リソース プロバイダーに登録するには、リソース プロバイダーを自分のサブスクリプションに登録する必要があります。 これは Azure portal、Azure CLI、または PowerShell を使用して行うことができます。 

### <a name="with-the-azure-portal"></a>Azure Portal の場合
以下の手順では、Azure portal を使用して SQL リソース プロバイダーを Azure サブスクリプションに登録します。 

1. Azure portal を開き、**すべてのサービス**に移動します。 
1. **サブスクリプション**に移動し、関心のあるサブスクリプションを選択します。  
1. **[サブスクリプション]** ブレードで、**[リソース プロバイダー]** に移動します。 
1. フィルター内に`sql`と入力し、SQL 関連のリソース プロバイダーを表示します。 
1. 目的となるアクションに応じて、*登録*、*再登録*、または*登録解除*のいずれかを **Microsoft.SqlVirtualMachine** プロバイダーで選択します。 

   ![プロバイダーの変更](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="with-azure-cli"></a>Azure CLI の場合
次のコード スニペットでは、SQL リソース プロバイダーが自分の Azure サブスクリプションに登録されます。 

```azurecli
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="with-powershell"></a>PowerShell の場合
次のコード スニペットでは、SQL リソース プロバイダーが自分の Azure サブスクリプションに登録されます。 

```powershell
# Register the new SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```


## <a name="register-sql-server-vm-with-sql-resource-provider"></a>SQL Server VM を SQL リソース プロバイダーに登録する
SQL リソース プロバイダーをサブスクリプションに登録したら、SQL Server VM をリソース プロバイダーに登録できます。 これは、Azure CLI または PowerShell を使用して行うことができます。 

### <a name="with-azure-cli"></a>Azure CLI の場合

Azure CLI で次のコード スニペットを使用して、SQL Server VM を登録します。 

```azurecli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```

### <a name="with-powershell"></a>PowerShell の場合

PowerShell で次のコード スニペットを使用して、SQL Server VM を登録します。 

```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

## <a name="change-licensing-model"></a>ライセンス モデルを変更する

SQL Server VM をリソース プロバイダーに登録したら、Azure portal、Azure CLI、または PowerShell を使用してライセンス モデルを変更できます。 

### <a name="with-the-azure-portal"></a>Azure Portal の場合

ライセンス モデルをポータルから直接変更できます。 

1. [Azure portal](https://portal.azure.com) 内で SQL Server VM に移動します。 
1. **[設定]** ウィンドウで、**[SQL Server の構成]** を選択します。 
1. **[SQL Server ライセンス]** ウィンドウで **[編集]** を選択して、ライセンスを変更します。 

![ポータルの AHB](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > このオプションは、ライセンス持ち込みイメージには使用できません。 

### <a name="with-azure-cli"></a>Azure CLI の場合

Azure CLI を使用して、ライセンス モデルを変更することができます。  

次のコード スニペットでは、従量課金制ライセンス モデルを BYOL (Azure ハイブリッド特典の使用) に切り替えます。

```azurecli
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

次のコード スニペットでは、BYOL モデルを従量課金制に切り替えます。 

```azurecli
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

### <a name="with-powershell"></a>PowerShell の場合 

PowerShell を使用してライセンス モデルを変更できます。 

次のコード スニペットでは、従量課金制ライセンス モデルを BYOL (Azure ハイブリッド特典の使用) に切り替えます。 

```PowerShell
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

```PowerShell
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


## <a name="view-current-licensing"></a>現在のライセンスの表示 

次のコード スニペットを使用すると、SQL Server VM の現在のライセンス モデルを表示できます。 

```powershell
# View current licensing model for your SQL Server VM
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>

$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="known-errors"></a>既知のエラー

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>SQL IaaS 拡張機能が仮想マシンにインストールされていない
SQL IaaS 拡張機能は、SQL Server VM を SQL VM リソース プロバイダーに登録するために必要な前提条件です。 SQL IaaS 拡張機能をインストールする前に SQL Server VM を登録しようとすると、次のエラーが発生します。

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

この問題を解決するには、SQL Server VM の登録を試みる前に、SQL IaaS 拡張機能をインストールします。 

  > [!NOTE]
  > SQL IaaS 拡張機能をインストールすると SQL Server サービスが再起動されるため、メンテナンス期間中にのみ実行する必要があります。 詳細については、[SQL IaaS 拡張機能のインストール](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation)に関するページを参照してください。 

### <a name="cannot-validate-argument-on-parameter-sku"></a>パラメーター 'Sku' の引数を検証できない
4.0 より後の Azure PowerShell を使用している場合に、SQL Server VM のライセンス モデルを変更しようとすると、このエラーが発生する可能性があります。

`Set-AzResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

このエラーを解決するには、ライセンス モデルを切り替えるときに、前に説明した PowerShell コード スニペットの以下の行のコメントを解除します。 
```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Azure PowerShell のバージョンを確認するには、次のコードを使用します。

```powershell
Get-Module -ListAvailable -Name Azure -Refresh
```

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>リソース グループ '<resource-group>' にリソース 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/<resource-group>' が見つかりませんでした。 このオブジェクトにプロパティ 'sqlServerLicenseType' が見つかりません。 プロパティが存在し、設定可能であることを確認してください。
このエラーは、SQL Server VM で SQL リソース プロバイダーに登録されていないライセンス モデルを変更しようとしたときに発生します。 リソース プロバイダーを[サブスクリプション](#register-sql-resource-provider-to-your-subscription)に登録した後、SQL Server VM を SQL [リソース プロバイダー](#register-sql-server-vm-with-sql-resource-provider)に登録する必要があります。 

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM における SQL Server に関するよくあるご質問](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM における SQL Server に関する料金ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM における SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)


