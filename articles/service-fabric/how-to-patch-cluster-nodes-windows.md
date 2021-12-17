---
title: Service Fabric クラスターでの Windows オペレーティング システムへのパッチの適用
description: Windows で実行されている Service Fabric クラスター ノードにパッチを適用するために、OS イメージの自動アップグレードを有効にする方法を次に示します。
ms.topic: how-to
ms.date: 10/19/2021
ms.openlocfilehash: 940a0af02f5355e9d28bb057798004fc963dfeca
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019402"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Service Fabric クラスターでの Windows オペレーティング システムへのパッチの適用

[Virtual Machine Scale Sets での OS イメージの自動アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)の取得は、Azure でオペレーティング システムにパッチが適用された状態を保つためのベスト プラクティスです。 仮想マシン スケール セット ベースの OS イメージの自動アップグレードでは、スケール セットに Silver 以上の耐久性が必要です。

## <a name="requirements-for-automatic-os-image-upgrades-by-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets による OS イメージの自動アップグレードの要件

- Service Fabric の[持続性レベル](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)は Silver または Gold であり、Bronze ではありません。
- スケール セット モデル定義の Service Fabric 拡張機能には、TypeHandlerVersion 1.1 以降が必要です。
- 持続性レベルは、スケール セット モデル定義の Service Fabric クラスターと Service Fabric 拡張機能で同じである必要があります。
- Virtual Machine Scale Sets に追加の正常性プローブやアプリケーション正常性拡張機能を使用する必要はありません。
- ステートレス ノードタイプは唯一の例外で、耐久性はブロンズですが、OS イメージの自動アップグレードは引き続き構成できます。 詳細については、「ステートレス専用ノード[タイプを使用して Azure Service Fabric クラスターをデプロイする」を参照してください](service-fabric-stateless-node-types.md)。

Service Fabric クラスターと Service Fabric 拡張機能で持続性の設定に不一致がないことを確認します。これは、一致しない場合にアップグレード エラーが発生する可能性があるためです。 持続性レベルは、[このページ](service-fabric-cluster-capacity.md#changing-durability-levels)で説明されているガイドラインに従って変更できます。

ブロンズ 持続性を使用する場合、OS イメージの自動アップグレードは使用できません。 [パッチ オーケストレーション アプリケーション](service-fabric-patch-orchestration-application.md) (Azure 以外でホストされているクラスターのみが対象) は、Silver 以上の持続性レベルでは *推奨されません* が、Service Fabric アップグレード ドメインに関しては、Windows 更新プログラムを自動化する唯一のオプションです。

パッチ オーケストレーション アプリケーションから OS イメージの自動アップグレードに切り替える場合、最初にパッチ オーケストレーション アプリケーションの使用を非推奨にする必要があります。

## <a name="enable-auto-os-upgrades-and-disable-windows-update"></a>OS の自動アップグレードを有効にし、Windows Update を無効にする

OS の自動更新を有効にする場合は、展開テンプレートで Windows Update を無効にする必要もあります。 これらの変更をデプロイすると、スケール セット内のすべてのマシンが再イメージ化され、スケール セットで自動更新が有効になります。

> [!IMPORTANT]
> Service Fabric では、OS ディスクを交換せずに、Windows Update によってオペレーティング システムのパッチを適用する VM 内アップグレードはサポートされていません。


1. OS イメージの自動アップグレードを有効にし、展開テンプレートで Windows 更新プログラムを無効にします。
 
    ```json
    "virtualMachineProfile": { 
        "properties": {
          "upgradePolicy": {
            "automaticOSUpgradePolicy": {
              "enableAutomaticOSUpgrade":  true
            }
          }
        }
      }
    ```
    
    ```json
    "virtualMachineProfile": { 
        "osProfile": { 
            "windowsConfiguration": { 
                "enableAutomaticUpdates": false 
            }
        }
    }
    ```

    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -AutomaticOSUpgrade $true -EnableAutomaticUpdate $false
    ```

1. スケール セット モデルを更新します。 この構成変更後、変更が有効になるように、スケール セット モデルを更新するには、すべてのマシンの再イメージ化が必要です。

    ```azurepowershell-interactive
    $scaleSet = Get-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName
    $instances = foreach($vm in $scaleSet)
    {
        Set-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -InstanceId $vm.InstanceID -Reimage
    }
    ```

## <a name="next-steps"></a>次のステップ

[Virtual Machine Scale Sets で OS イメージの自動アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)を有効にする方法について説明します。