---
title: Azure での VM とスケール セットの拡張機能の自動アップグレード
description: Azure で仮想マシンと仮想マシン スケール セットの拡張機能の自動アップグレードを有効にする方法について説明します。
author: mayanknayar
ms.service: virtual-machines
ms.subservice: extensions
ms.workload: infrastructure
ms.topic: how-to
ms.date: 08/10/2021
ms.author: manayar
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a7368ee52808dc56e3532f2edfb41e00c7737a7e
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216463"
---
# <a name="automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Azure での VM とスケール セットの拡張機能の自動アップグレード

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

拡張機能の自動アップグレードは、Azure VM と Azure Virtual Machine Scale Sets で利用できます。 VM またはスケール セットで拡張機能の自動アップグレードが有効になっていると、拡張機能の発行元がその拡張機能の新しいバージョンをリリースするたびに、拡張機能が自動的にアップグレードされます。

 拡張機能の自動アップグレードには、次の機能があります。
- Azure VM と Azure Virtual Machine Scale Sets でサポートされています。
- アップグレードは、可用性優先のデプロイ モデルで適用されます (後で詳述)。
- Virtual Machine Scale Sets では、スケール セットの仮想マシンの 20% 以上が 1 回のバッチでアップグレードされます。 最小バッチ サイズは 1 仮想マシンです。
- すべての VM サイズ、および Windows と Linux の両方の拡張機能で動作します。
- いつでも自動アップグレードをオプトアウトできます。
- 拡張機能の自動アップグレードは、任意のサイズの Virtual Machine Scale Sets で有効にすることができます。
- サポートされている各拡張機能は個別に登録され、どの拡張機能を自動的にアップグレードするかを選択できます。
- すべてのパブリック クラウド リージョンでサポートされています。

## <a name="how-does-automatic-extension-upgrade-work"></a>拡張機能の自動アップグレードのしくみ
拡張機能のアップグレード プロセスにより、VM 上の既存の拡張機能のバージョンが、拡張機能の発行元によって発行された、同じ拡張機能の新しいバージョンに置き換えられます。 新しい拡張機能がインストールされた後、VM の正常性が監視されます。 アップグレードが完了してから 5 分以内に VM が正常な状態にならない場合、拡張機能のバージョンは前のバージョンにロールバックされます。

拡張機能の更新が失敗した場合は、自動的に再試行されます。 再試行は、ユーザーの介入なしに、数日に 1 回試行されます。

### <a name="availability-first-updates"></a>可用性優先の更新
プラットフォームの調整された更新の可用性優先モデルにより、Azure の可用性構成が複数の可用性レベルで尊重されます。

更新中の仮想マシンのグループについては、Azure プラットフォームで更新がオーケストレーションされます。

**複数のリージョン間:**
- Azure 全体のデプロイが失敗するのを防ぐために、更新は Azure 全体を段階的に移動します。
- 1 つの "フェーズ" には 1 つ以上のリージョンを含めることができます。前のフェーズで対象の VM が正常に更新された場合にのみ、更新はフェーズ間を移動します。
- geo ペア リージョンは同時に更新されず、同じリージョン フェーズに置くことはできません。
- 更新の成功は、更新後に VM の正常性を追跡することによって測定できます。 VM の正常性は、VM のプラットフォーム正常性インジケーターによって追跡されます。 Virtual Machine Scale Sets の場合、VM の正常性は、アプリケーション正常性プローブまたはアプリケーション正常性拡張機能によって追跡されます (スケール セットに適用される場合)。

**リージョン内:**
- 異なる Availability Zones の VM は、同じ更新で同時に更新されることはありません。
- 可用性セットの一部ではない個々の VM は、サブスクリプション内のすべての VM が同時に更新されることを回避するために、ベスト エフォート方式でバッチ処理されます。  

**"セット" 内:**
- 共通の可用性セット内またはスケール セット内のすべての VM が同時に更新されることはありません。  
- 共通の可用性セット内の VM は更新ドメインの境界内で更新され、複数の更新ドメインにまたがる VM は同時に更新されません。  
- 共通の仮想マシン スケール セット内の VM は、バッチにグループ化され、更新ドメインの境界内で更新されます。

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets のアップグレード プロセス
1. アップグレード プロセスの開始前に、オーケストレーターにより、(何らかの理由で) 異常な VM がスケール セット全体の 20% を超えていないことが確認されます。

2. アップグレード オーケストレーターによってアップグレードする VM インスタンス バッチが識別されます。 アップグレード バッチには、仮想マシンの最小バッチ サイズに基づき、VM 合計数の最大 20% が入ります。

3. アプリケーション正常性プローブまたはアプリケーション正常性拡張機能が構成されているスケール セットの場合、アップグレードは VM が正常になるまで 5 分間 (または定義されている正常性プローブ構成だけ) 待機した後、次のバッチがアップグレードされます。 アップグレード後に VM の正常性が回復しない場合は、既定で、VM の前のバージョンの拡張機能が再インストールされます。

4. また、アップグレード オーケストレーターにより、アップグレード後に異常が発生した VM の割合も追跡されます。 アップグレード処理中に異常なアップグレード済みインスタンスの割合が 20% を超えた場合、アップグレードは停止します。

上記のプロセスは、スケール セット内のすべてのインスタンスがアップグレードされるまで続行されます。

スケール セットのアップグレード オーケストレーターにより、各バッチをアップグレードする前に、スケール セット全体の正常性が確認されます。 バッチのアップグレード中に、他の計画メンテナンスまたは計画外メンテナンスのアクティビティが同時に発生することがあり、それによってスケール セット仮想マシンの正常性が影響を受ける場合があります。 そのような場合、スケール セットのインスタンスの 20% より多くが異常な状態になると、スケール セットのアップグレードは現在のバッチが終了した時点で停止します。

## <a name="supported-extensions"></a>サポートされる拡張機能
拡張機能の自動アップグレードでは、次の拡張機能がサポートされています (さらに多くの拡張機能が定期的に追加されます)。
- Dependency Agent – [Linux](./extensions/agent-dependency-linux.md) および [Windows](./extensions/agent-dependency-windows.md)
- [アプリケーション正常性拡張機能](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) – Linux および Windows
- [ゲスト構成拡張機能](./extensions/guest-configuration.md) – Linux および Windows
- Key Vault – [Linux](./extensions/key-vault-linux.md) および [Windows](./extensions/key-vault-windows.md)


## <a name="enabling-automatic-extension-upgrade"></a>拡張機能の自動アップグレードの有効化

拡張機能の自動アップグレードを拡張機能に対して有効にするには、プロパティ `enableAutomaticUpgrade` が `true` に設定され、各拡張機能の定義に個別に追加されていることを確認する必要があります。

### <a name="rest-api-for-virtual-machines"></a>Virtual Machines の REST API
Azure VM で拡張機能に対して拡張機能の自動アップグレードを有効にするには (この例では Dependency Agent 拡張機能)、次のようにします。

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets の REST API
スケール セット モデルに拡張機能を追加するには、次のようにします。

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>Virtual Machines の Azure PowerShell
[Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) コマンドレットを使用します。

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets の Azure PowerShell
拡張機能をスケール セット モデルに追加するには、[Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) コマンドレットを使用します。

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

拡張機能を追加した後、[Update-AzVmss](/powershell/module/az.compute/update-azvmss) を使用してスケール セットを更新します。


### <a name="azure-cli-for-virtual-machines"></a>Virtual Machines 向け Azure CLI
[az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) コマンドレットを使用します。

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets の Azure CLI
拡張機能をスケール セット モデルに追加するには、[az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) コマンドレットを使用します。

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>複数の拡張機能での拡張機能のアップグレード

VM または Virtual Machine Scale Set に複数の拡張機能を追加し、その拡張機能の自動アップグレードを有効にできます。 同じ VM またはスケール セットに他の拡張機能を追加し、その拡張機能自動アップグレードを有効にしないこともできます。  

1 つの仮想マシンで複数の拡張機能アップグレードが利用できる場合、アップグレードをバッチにまとめることができる場合がありますが、仮想マシン上では、各拡張機能アップグレードが個別に適用されます。 1 つの拡張機能で障害が発生しても、アップグレードされる可能性のある他の拡張機能には影響しません。 たとえば、2 つの拡張機能のアップグレードがスケジュールされていて、最初の拡張機能のアップグレードが失敗した場合でも、2 つ目の拡張機能はアップグレードされます。

VM または仮想マシン スケール セットに[拡張機能のシーケンス処理](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md)を使用して複数の拡張機能が構成されている場合は、拡張機能の自動アップグレードも適用できます。 拡張機能のシーケンス処理は VM の初回デプロイに適用されます。将来、ある拡張機能に拡張機能アップグレードがあるとすれば、それは個別に適用されます。


## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [アプリケーション正常性拡張機能について確認する](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
