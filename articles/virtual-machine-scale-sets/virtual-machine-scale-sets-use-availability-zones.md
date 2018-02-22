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
ms.date: 01/11/2018
ms.author: iainfou
ms.openlocfilehash: 2de214f604469025a8a4accde44359fea0ded7e9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>可用性ゾーンを使用する仮想マシン スケール セットを作成する (プレビュー)
データセンター レベルの障害から仮想マシン スケール セットを保護するには、複数の可用性ゾーンにまたがるスケール セットを作成できます。 可用性ゾーンをサポートする Azure リージョンには少なくとも 3 つの異なるゾーンがあり、それぞれが独自の独立した電源、ネットワーク、冷却装置を備えています。 詳細については、[可用性ゾーンの概要](../availability-zones/az-overview.md)に関するページをご覧ください。

[!INCLUDE [availability-zones-preview-statement.md](../../includes/availability-zones-preview-statement.md)]


## <a name="single-zone-and-zone-redundant-scale-sets"></a>単一ゾーン スケール セットとゾーン冗長スケール セット
仮想マシン スケール セットを展開するときは、1 つのリージョン内の単一の可用性ゾーンまたは複数のゾーンを使うことができます。

単一のゾーンにスケール セットを作成する場合は、すべての VM インスタンスが実行するゾーンをユーザーが制御し、スケール セットはそのゾーン内でのみ管理および自動スケールされます。 ゾーン冗長スケール セットを使うと、複数のゾーンにまたがる単一のスケール セットを作成できます。 VM インスタンスが作成されると、既定で、複数のゾーンに均等に分散されます。 ゾーンの 1 つで中断が発生しても、スケール セットは容量を増やすためのスケールアウトを自動的には行いません。 CPU またはメモリの使用率に基づく自動スケール ルールを構成するのがベスト プラクティスです。 自動スケール ルールを作成すると、スケール セットは、その 1 つのゾーンでの VM インスタンスの喪失に、残りの動作しているゾーンに新しいインスタンスをスケールアウトすることによって、対応できるようになります。

可用性ゾーンを使うには、[サポートされている Azure リージョン](../availability-zones/az-overview.md#regions-that-support-availability-zones)にスケール セットを作成する必要があります。 また、[可用性ゾーン (プレビュー) に登録する](http://aka.ms/azenroll)必要もあります。 次のいずれかの方法で、可用性ゾーンを使うスケール セットを作成できます。

- [Azure Portal](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Azure リソース マネージャーのテンプレート](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Azure ポータルの使用
可用性ゾーンを使うスケール セットを作成するプロセスは、[使用の開始に関する記事](virtual-machine-scale-sets-create-portal.md)で詳しく説明されているものと同じです。 [可用性ゾーン (プレビュー) に登録](http://aka.ms/azenroll)していることを確認します。 次の例で示すように、サポートされている Azure リージョンを選ぶときに、使用可能なゾーンの 1 つにスケール セットを作成できます。

![単一の可用性ゾーンにスケール セットを作成する](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

スケール セットと、Azure ロード バランサーやパブリック IP アドレスなどのそれをサポートするリソースは、指定した 1 つのゾーンに作成されます。


## <a name="use-the-azure-cli-20"></a>Azure CLI 2.0 を使用する
可用性ゾーンを使うスケール セットを作成するプロセスは、[使用の開始に関する記事](virtual-machine-scale-sets-create-cli.md)で詳しく説明されているものと同じです。 可用性ゾーンを使うには、サポートされている Azure リージョンにスケール セットを作成し、[可用性ゾーン (プレビュー) に登録](http://aka.ms/azenroll)する必要があります。

`--zones` パラメーターを [az vmss create](/cli/azure/vmss#az_vmss_create) コマンドに追加して、使うゾーンを指定します (ゾーン *1*、*2*、*3* など)。 次の例では、*myScaleSet* という名前の単一ゾーン スケール セットをゾーン *1* に作成します。

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
単一ゾーン スケール セットとネットワーク リソースの完全な例については、[こちらのサンプル CLI スクリプト](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.)をご覧ください

### <a name="zone-redundant-scale-set"></a>ゾーン冗長スケール セット
ゾーン冗長スケールを作成するには、*Standard* SKU のパブリック IP アドレスとロード バランサーを使います。 冗長性を高めるため、*Standard* SKU はゾーン冗長ネットワーク リソースを作成します。 詳しくは、「[Azure Load Balancer Standard の概要](../load-balancer/load-balancer-standard-overview.md)」をご覧ください。 ゾーン冗長スケール セットまたはロード バランサーを初めて作成するときは、次の手順を行って、これらのプレビュー機能にアカウントを登録する必要があります。

1. 次のように、[az feature register](/cli/azure/feature#az_feature_register) を使ってアカウントをゾーン冗長スケール セットおよびネットワーク機能に登録します。

    ```azurecli
    az feature register --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. 機能への登録には数分かかる場合があります。 操作の状態は、[az feature show](/cli/azure/feature#az_feature_show) で確認できます。

    ```azurecli
    az feature show --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```

    次に示す例では、*Registered* が機能の望ましい状態です。
    
    ```json
    "properties": {
          "state": "Registered"
       },
    ```

3. ゾーン冗長スケール セットとネットワーク リソースの両方で状態が *Registered* になったら、次のように、[az provider register](/cli/azure/provider#az_provider_register) を使って *Compute* プロバイダーと *Network* プロバイダーを再登録します。

    ```azurecli
    az provider register --namespace Microsoft.Compute
    az provider register --namespace Microsoft.Network
    ```

ゾーン冗長スケール セットを作成するには、`--zones` パラメーターで複数のゾーンを指定します。 次の例では、*myScaleSet* という名前のゾーン冗長スケール セットを、ゾーン *1、2、3* にまたがって作成します。

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones {1,2,3}
```

指定したゾーンにスケール セットのすべてのリソースと VM が作成および構成されるのに、数分かかります。 ゾーン冗長スケール セットとネットワーク リソースの完全な例については、[こちらのサンプル CLI スクリプト](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)をご覧ください


## <a name="use-azure-powershell"></a>Azure PowerShell の使用
可用性ゾーンを使うスケール セットを作成するプロセスは、[使用の開始に関する記事](virtual-machine-scale-sets-create-powershell.md)で詳しく説明されているものと同じです。 可用性ゾーンを使うには、サポートされている Azure リージョンにスケール セットを作成し、[可用性ゾーン (プレビュー) に登録](http://aka.ms/azenroll)する必要があります。 `-Zone` パラメーターを [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) コマンドに追加して、使うゾーンを指定します (ゾーン *1*、*2*、*3* など)。 

次の例では、*vmssConfig* という名前の単一ゾーン スケール セット構成を、"*米国東部 2*" のゾーン *1* に作成します。

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

単一ゾーン スケール セットとネットワーク リソースの完全な例については、[こちらのサンプル PowerShell スクリプト](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1)をご覧ください

### <a name="zone-redundant-scale-set"></a>ゾーン冗長スケール セット
ゾーン冗長スケールを作成するには、*Standard* SKU のパブリック IP アドレスとロード バランサーを使います。 冗長性を高めるため、*Standard* SKU はゾーン冗長ネットワーク リソースを作成します。 詳しくは、「[Azure Load Balancer Standard の概要](../load-balancer/load-balancer-standard-overview.md)」をご覧ください。 ゾーン冗長スケール セットまたはロード バランサーを初めて作成するときは、次の手順を行って、これらのプレビュー機能にアカウントを登録する必要があります。

1. 次のように、[Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) を使ってアカウントをゾーン冗長スケール セットおよびネットワーク機能に登録します。

    ```powershell
    Register-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. 機能への登録には数分かかる場合があります。 操作の状態は、[Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) で確認できます。

    ```powershell
    Get-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute 
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    次に示す例では、*Registered* が機能の望ましい状態です。
    
    ```powershell
    RegistrationState
    -----------------
    Registered
    ```

3. ゾーン冗長スケール セットとネットワーク リソースの両方で状態が *Registered* になったら、次のように、[Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) を使って *Compute* プロバイダーと *Network* プロバイダーを再登録します。

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```

ゾーン冗長スケール セットを作成するには、`-Zone` パラメーターで複数のゾーンを指定します。 次の例では、*myScaleSet* という名前のゾーン冗長スケール セット構成を、"*米国東部 2*" のゾーン *1、2、3* にまたがって作成します。

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1", "2", "3"
```

[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) でパブリック IP アドレスを作成する場合、または[New-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/New-AzureRmLoadBalancer) でロード バランサーを作成する場合、ゾーン冗長ネットワーク リソースを作成するには *-SKU "Standard"* を指定します。 また、ネットワーク セキュリティ グループとルールを作成して、すべてのトラフィックを許可する必要があります。 詳しくは、「[Azure Load Balancer Standard の概要](../load-balancer/load-balancer-standard-overview.md)」をご覧ください。

ゾーン冗長スケール セットとネットワーク リソースの完全な例については、[こちらのサンプル PowerShell スクリプト](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1)をご覧ください


## <a name="use-azure-resource-manager-templates"></a>Azure リソース マネージャー テンプレートの使用
可用性ゾーンを使うスケール セットを作成するプロセスは、[Linux](virtual-machine-scale-sets-create-template-linux.md) または [Windows](virtual-machine-scale-sets-create-template-windows.md) での使用の開始に関する記事で詳しく説明されているものと同じです。 可用性ゾーンを使うには、サポートされている Azure リージョンにスケール セットを作成し、[可用性ゾーン (プレビュー) に登録](http://aka.ms/azenroll)する必要があります。 テンプレートの *Microsoft.Compute/virtualMachineScaleSets* のリソースの種類に `zones` プロパティを追加し、使うゾーンを指定します (ゾーン *1*、*2*、*3* など)。

次の例では、*myScaleSet* という名前の Linux 単一ゾーン スケール セットを、"*米国東部 2*" のゾーン *1* に作成します。

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

単一ゾーン スケール セットとネットワーク リソースの完全な例については、[こちらのサンプル Resource Manager テンプレート](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)をご覧ください

### <a name="zone-redundant-scale-set"></a>ゾーン冗長スケール セット
ゾーン冗長スケール セットを作成するには、リソースの種類 *Microsoft.Compute/virtualMachineScaleSets* の `zones` プロパティで複数の値を指定します。 次の例では、*myScaleSet* という名前のゾーン冗長スケール セットを、"*米国東部 2*" のゾーン *1、2、3* にまたがって作成します。

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

パブリック IP アドレスまたはロード バランサーを作成する場合、ゾーン冗長ネットワーク リソースを作成するには、*"sku": { "name": "Standard" }"* プロパティを指定します。 また、ネットワーク セキュリティ グループとルールを作成して、すべてのトラフィックを許可する必要があります。 詳しくは、「[Azure Load Balancer Standard の概要](../load-balancer/load-balancer-standard-overview.md)」をご覧ください。

ゾーン冗長スケール セットとネットワーク リソースの完全な例については、[こちらのサンプル Resource Manager テンプレート](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)をご覧ください


## <a name="next-steps"></a>次の手順
可用性ゾーンにスケール セットを作成したので、次に、[仮想マシン スケール セットにアプリケーションを展開する](virtual-machine-scale-sets-deploy-app.md)方法または[仮想マシン スケール セットで自動スケールを使用する](virtual-machine-scale-sets-autoscale-overview.md)方法を学習できます。
