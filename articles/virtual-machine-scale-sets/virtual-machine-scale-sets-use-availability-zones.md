---
title: "可用性ゾーンを使用する Azure スケール セットを作成する (プレビュー) | Microsoft Docs"
description: "障害に対する冗長性を高めるために可用性ゾーンを使用する Azure 仮想マシン スケール セットを作成する方法について説明します"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: iainfou
ms.openlocfilehash: 310fdc68d3eb662906053d2bc0c45e6cfa18d4da
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>可用性ゾーンを使用する仮想マシン スケール セットを作成する (プレビュー)
データセンター レベルの障害から仮想マシン スケール セットを保護するには、可用性ゾーンにスケール セットを作成できます。 可用性ゾーンをサポートする Azure リージョンには少なくとも 3 つの異なるゾーンがあり、それぞれが独自の独立した電源、ネットワーク、冷却装置を備えています。 詳細については、[可用性ゾーンの概要](../availability-zones/az-overview.md)に関するページをご覧ください。

可用性ゾーンを使うには、[サポートされている Azure リージョン](../availability-zones/az-overview.md#regions-that-support-availability-zones)にスケール セットを作成する必要があります。 次のいずれかの方法で、可用性ゾーンを使うスケール セットを作成できます。

- [Azure Portal](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Azure リソース マネージャーのテンプレート](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Azure ポータルの使用
可用性ゾーンを使うスケール セットを作成するプロセスは、[使用の開始に関する記事](virtual-machine-scale-sets-create-portal.md)で詳しく説明されているものと同じです。 次の例で示すように、サポートされている Azure リージョンを選ぶときに、使用可能なゾーンの 1 つにスケール セットを作成できます。

![単一の可用性ゾーンにスケール セットを作成する](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

スケール セットと、Azure ロード バランサーやパブリック IP アドレスなどのそれをサポートするリソースは、指定した 1 つのゾーンに作成されます。


## <a name="use-the-azure-cli-20"></a>Azure CLI 2.0 を使用する
可用性ゾーンを使うスケール セットを作成するプロセスは、[使用の開始に関する記事](virtual-machine-scale-sets-create-cli.md)で詳しく説明されているものと同じです。 可用性ゾーンを使うには、サポートされている Azure リージョンにスケール セットを作成する必要があります。 `--zones` パラメーターを [az vmss create](/cli/azure/vmss#az_vmss_create) コマンドに追加して、使うゾーンを指定します (ゾーン *1*、*2*、*3* など)。 次の例では、*myScaleSet* という名前のスケール セットをゾーン *1* に作成します。

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

指定したゾーンにスケール セットのすべてのリソースと VM が作成および構成されるのに、数分かかります。


## <a name="use-azure-powershell"></a>Azure PowerShell の使用
可用性ゾーンを使うスケール セットを作成するプロセスは、[使用の開始に関する記事](virtual-machine-scale-sets-create-powershell.md)で詳しく説明されているものと同じです。 可用性ゾーンを使うには、サポートされている Azure リージョンにスケール セットを作成する必要があります。 `-Zone` パラメーターを [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) コマンドに追加して、使うゾーンを指定します (ゾーン *1*、*2*、*3* など)。 次の例では、*vmssConfig* という名前のスケール セット構成を、"*米国東部 2*" のゾーン *1* に作成します。

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

実際のスケール セットを作成するには、[使用の開始に関する記事](virtual-machine-scale-sets-create-powershell.md)で詳しく説明されている手順に従ってください。


## <a name="use-azure-resource-manager-templates"></a>Azure リソース マネージャー テンプレートの使用
可用性ゾーンを使うスケール セットを作成するプロセスは、[Linux](virtual-machine-scale-sets-create-template-linux.md) または [Windows](virtual-machine-scale-sets-create-template-windows.md) での使用の開始に関する記事で詳しく説明されているものと同じです。 可用性ゾーンを使うには、サポートされている Azure リージョンにスケール セットを作成する必要があります。 テンプレートの *Microsoft.Compute/virtualMachineScaleSets* のリソースの種類に `zones` プロパティを追加し、使うゾーンを指定します (ゾーン *1*、*2*、*3* など)。 次の例では、*myScaleSet* という名前の Linux スケール セットを、"*米国東部 2*" のゾーン *1* に作成します。

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

実際のスケール セットを作成するには、[Linux](virtual-machine-scale-sets-create-template-linux.md) または [Windows](virtual-machine-scale-sets-create-template-windows.md) での使用の開始に関する記事で詳しく説明されている手順に従ってください


## <a name="next-steps"></a>次の手順
可用性ゾーンにスケール セットを作成したので、次に、[仮想マシン スケール セットにアプリケーションを展開する](virtual-machine-scale-sets-deploy-app.md)方法または[仮想マシン スケール セットで自動スケールを使用する](virtual-machine-scale-sets-autoscale-overview.md)方法を学習できます。
