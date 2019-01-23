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
ms.date: 11/14/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1b1c7192eb8389d3ad3a1c7c935d9c7e2d8769a9
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359920"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Azure での SQL Server 仮想マシンのライセンス モデルを変更する方法
このアーティクルでは、新しい SQL VM リソース プロバイダーである、**Microsoft.SqlVirtualMachine** を使用して Azure での SQL Server 仮想マシンのライセンス モデルを変更する方法を説明します。 SQL Server をホスティングする仮想マシン (VM) には、従量課金モデルおよびご自身のライセンス持ち込み (BYOL) の 2 種類があります。 現在では、PowerShell または Azure CLI を使用して、SQL Server VM でどちらのライセンス モデルを使用するかを変更できます。 

**従量課金**モデルでは、Azure VM を実行する秒単位のコストに SQL Server ライセンスのコストが含まれます。

**ご自身のライセンス持ち込み**モデルは[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/) としても知られ、SQL Server を実行する VM に伴うお客様ご自身の SQL Server ライセンスを使用することができます。 価格の詳細については、[SQL Server VM 料金ガイド](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)をご参照ください。

2 種類のライセンスモデル間の切り替えによる**ダウンタイム**および VM の再起動は、**追加コスト**は発生せず (実際には、AHB の有効化によるコスト*削減*)、**ただちに有効となります**。 

## <a name="prerequisites"></a>前提条件
SQL VM リソース プロバイダーを利用するには、SQL IaaS 拡張機能が必要です。 そのため、SQL VM リソース プロバイダーの利用を続けるには、以下が必要です。
- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- [SQL Server IaaS 拡張機能](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)がインストールされている [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 


## <a name="register-existing-sql-server-vm-with-new-resource-provider"></a>既存の SQL Server VM を新しいリソース プロバイダーに登録する
ライセンス モデル間の切り替えを可能とする能力は、新しい SQL VM リソース プロバイダー (Microsoft.SqlVirtualMachine) によって提供される機能です。 2018 年 12 月以降にデプロイされた SQL Server VM は、自動的に新しいリソース プロバイダーに登録します。 ただし、この日付より前にデプロイされた既存の VM は、リソース プロバイダーに手動で登録しないと、ライセンス モデルの切り替えが可能になりません。 




  > SQL VM リソースを削除する場合は、イメージにハード コーディングされたライセンス設定に戻ります。 


### <a name="powershell"></a>PowerShell

次のコード スニペットは、ユーザーを Azure に接続し、ユーザーがどのサブスクリプション ID を使用しているかを確認します。 
```PowerShell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>
```

次のコード スニペットは、まず新しい SQL リソース プロバイダーをサブスクリプションに登録してから、既存の SQL Server VM を新しいリソース プロバイダーに登録します。 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine


# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>ポータル
ポータルを使用して新しい VM の SQL リソース プロバイダーを登録することもできます。 これを行うには、次の手順に従います。
1. Azure portal を開き、**すべてのサービス**に移動します。 
1. **サブスクリプション**に移動し、関心のあるサブスクリプションを選択します。  
1. **サブスクリプション** ブレード内で、**リソースプロバイダー**に移動します。 
1. フィルター内に`sql`と入力し、SQL 関連のリソース プロバイダーを表示します。 
1. 目的となるアクションに応じて、*登録*、*再登録*、または*登録解除*のいずれかを **Microsoft.SqlVirtualMachine** プロバイダーで選択します。 

  ![プロバイダーの変更](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>PowerShell の使用 
PowerShell を使用してライセンス モデルを変更できます。  ライセンス モデルを切り替える前に、SQL Server VM が新しい SQL リソース プロバイダーで既に登録されていることを確認します。 

次のコード スニペットは、従量課金ライセンス モデルを BYOL (または、Azure ハイブリッド特典の使用) に切り替えます。 
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
``` 

次のコード スニペットは、BYOL モデルを従量課金に切り替えます。
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > ライセンスを切り替えるには、新しい SQL VM のリソース プロバイダーを使用する必要があります。 SQL Server VM を新しいプロバイダーに登録する前にこれらのコマンドを実行しようとすると、次のエラーが発生する場合があります。`Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` このエラーが表示された場合は、[SQL Server VM を新しいリソース プロバイダーに登録](#register-existing-SQL-vm-with-new-resource-provider)してください。 
 

## <a name="use-azure-cli"></a>Azure CLI の使用
Azure CLI を使用して、ライセンス モデルを変更することができます。  ライセンス モデルを切り替える前に、SQL Server VM が新しい SQL リソース プロバイダーで既に登録されていることを確認します。 

次のコード スニペットは、従量課金ライセンス モデルを BYOL (または、Azure ハイブリッド特典の使用) に切り替えます。
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

次のコード スニペットは、BYOL モデルを従量課金に切り替えます。 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >ライセンスを切り替えるには、新しい SQL VM のリソース プロバイダーを使用する必要があります。 SQL Server VM を新しいプロバイダーに登録する前にこれらのコマンドを実行しようとすると、次のエラーが発生する場合があります。`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` このエラーが表示された場合は、[SQL Server VM を新しいリソース プロバイダーに登録](#register-existing-SQL-vm-with-new-resource-provider)してください。 

## <a name="view-current-licensing"></a>現在のライセンスの表示 

次のコード スニペットを使用すると、SQL Server VM の現在のライセンス モデルを表示できます。 

```PowerShell
# View current licensing model for your SQL Server VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
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

`Set-AzureRmResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

このエラーを解決するには、ライセンス モデルを切り替えるときに、前に説明した PowerShell コード スニペットの以下の行のコメントを解除します。 
```PowerShell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Azure PowerShell のバージョンを確認するには、次のコードを使用します。

```PowerShell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM における SQL Server に関するよくあるご質問](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM における SQL Server に関する料金ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM における SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)


