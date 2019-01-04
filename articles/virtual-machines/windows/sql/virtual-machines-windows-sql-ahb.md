---
title: Azure で SQL VM のライセンス モデルを変更する方法 |Microsoft Docs
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
ms.openlocfilehash: cd784163047f4fe15fde719ce56aba64eed60dd2
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336987"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Azure での SQL Server 仮想マシンのライセンス モデルを変更する方法
このアーティクルでは、新しい SQL リソース プロバイダーである、**Microsoft.SqlVirtualMachine** を使用して Azure での SQL Server 仮想マシンのライセンス モデルを変更する方法を説明します。 SQL Server をホスティングする仮想マシン (VM) には、従量課金モデルおよびご自身のライセンス持ち込み (BYOL) の 2 種類があります。 現在では、PowerShell または Azure CLI を使用して、SQL VM でどちらのライセンス モデルを使用するかを変更できます。 

**従量課金**モデルでは、Azure VM を実行する秒単位のコストに SQL Server ライセンスのコストが含まれます。

**ご自身のライセンス持ち込み**モデルは[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/) としても知られ、SQL Server を実行する VM に伴うお客様ご自身の SQL Server ライセンスを使用することができます。 価格の詳細については、[SQL VM 料金ガイド](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)をご参照ください。

2 種類のライセンスモデル間の切り替えによる**ダウンタイム**および VM の再起動は、**追加コスト**は発生せず (実際には、AHB の有効化によるコスト削減)、 **ただちに有効となります**。 


## <a name="register-existing-sql-vm-with-new-resource-provider"></a>既存の SQL VM を新しいリソース プロバイダーに登録する
ライセンス モデル間の切り替えを可能とする能力は、新しい SQL VM リソース プロバイダー (Microsoft.SqlVirtualMachine) によって提供される機能です。 現時点では、ライセンス モデルを切り替えるには、まず新しいプロバイダーをサブスクリプションに登録してから、既存の VM を新しい SQL VM リソース プロバイダーに登録する必要があります。 SQL VM リソース プロバイダーを利用するには、SQL IaaS 拡張機能もインストールする必要があります。 それにより、VHD でデプロイされた VM を登録できるようになります。 詳細については、[SQL IaaS 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に関するページを参照してください。 

  >[!IMPORTANT]
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

次のコード スニペットは、まず新しい SQL リソース プロバイダーをサブスクリプションに登録してから、既存の SQL VM を新しいリソース プロバイダーに登録します。 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

# Register your existing SQL VM with the new resource provider
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
PowerShell を使用してライセンス モデルを変更できます。  ライセンス モデルを切り替える前に、SQL VM が新しい SQL リソース プロバイダーで既に登録されていることを確認します。 

次のコード スニペットは、従量課金ライセンス モデルを BYOL (または、Azure ハイブリッド特典の使用) に切り替えます。 
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
$SqlVm | Set-AzureRmResource -Force 
``` 

次のコード スニペットは、BYOL モデルを従量課金に切り替えます。
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > ライセンスを切り替えるには、新しい SQL VM のリソース プロバイダーを使用する必要があります。 SQL VM を新しいプロバイダーに登録する前にこれらのコマンドを実行しようとすると、次のエラーが発生する場合があります。`Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` このエラーが表示された場合は、[SQL VM を新しいリソース プロバイダーに登録](#register-existing-SQL-vm-with-new-resource-provider)してください。 
 

## <a name="use-azure-cli"></a>Azure CLI の使用
Azure CLI を使用して、ライセンス モデルを変更することができます。  ライセンス モデルを切り替える前に、SQL VM が新しい SQL リソース プロバイダーで既に登録されていることを確認します。 

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
  >ライセンスを切り替えるには、新しい SQL VM のリソース プロバイダーを使用する必要があります。 SQL VM を新しいプロバイダーに登録する前にこれらのコマンドを実行しようとすると、次のエラーが発生する場合があります。`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` このエラーが表示された場合は、[SQL VM を新しいリソース プロバイダーに登録](#register-existing-SQL-vm-with-new-resource-provider)してください。 

## <a name="view-current-licensing"></a>現在のライセンスの表示 

次のコード スニペットでは、SQL VM の現在のライセンス モデルを表示できます。 

```PowerShell
# View current licensing model for your SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM における SQL Server に関するよくあるご質問](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM における SQL Server に関する料金ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM における SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)


