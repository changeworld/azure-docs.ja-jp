---
title: CLI を使用して Azure Spot Virtual Machines をデプロイする
description: CLI を使用して Azure Spot Virtual Machines をデプロイすることでコストを節約する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 0a7be682f921efdfae486e8f6545758964a941ae
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098861"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Azure CLI を使用して Azure Spot Virtual Machines をデプロイする

[Azure Spot Virtual Machines](../spot-vms.md) を使用すると、大幅にコストを削減して未使用の容量を利用できます。 Azure で容量の回復が必要になると常に、Azure インフラストラクチャによって Azure スポット仮想マシンが削除されます。 したがって、Azure スポット仮想マシンは、バッチ処理ジョブ、開発/テスト環境、大規模なコンピューティング ワークロードなど、中断してもかまわないワークロードに最適です。

Azure スポット仮想マシンの価格は、リージョンと SKU に基づいて変動します。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) と [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の VM 価格を参照してください。 

VM に対して、1 時間あたりに支払うことができる最大価格を設定するオプションがあります。 Azure スポット仮想マシンの最大価格は、小数点以下最大 5 桁を使用して米ドル (USD) で設定できます。 たとえば、`0.98765` の値は、1 時間あたり $0.98765 米ドルの最大価格になります。 最大価格を `-1` に設定した場合、VM は価格に基づいて削除されません。 VM の価格は、使用可能な容量とクォータがある限り、現在の Azure Spot Virtual Machine の価格または標準 VM の価格のいずれか低い方になります。 最大価格の設定の詳細については、[Azure Spot Virtual Machines の価格](../spot-vms.md#pricing)に関するページを参照してください。

Azure CLI を使用して Azure Spot Virtual Machine を作成するプロセスは、[クイックスタートの記事](./quick-create-cli.md)で詳しく説明されている内容と同じです。 '--priority Spot' パラメーターを追加し、`--eviction-policy` を Deallocate (既定値) または `Delete` に設定して、最大価格または `-1` を指定するだけです。 


## <a name="install-azure-cli"></a>Azure CLI のインストール

Azure Spot Virtual Machines を作成するには、Azure CLI バージョン 2.0.74 以降が実行されている必要があります。 バージョンを確認するには、**az --version** を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

[az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。

```azurecli
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Azure Spot Virtual Machine を作成する

この例では、価格に基づいて削除されない Linux Azure Spot Virtual Machine をデプロイする方法を示しています。 この VM の割り当てを解除するように削除ポリシーが設定されているため、後で再起動することができます。 VM が削除されるときに VM と基になるディスクを削除する場合は、`--eviction-policy` を `Delete` に設定します。

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



VM が作成されたら、クエリを実行して、リソース グループ内のすべての VM の最大請求価格を確認できます。

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>削除をシミュレートする

Azure スポット仮想マシンの[削除をシミュレート](/rest/api/compute/virtualmachines/simulateeviction)して、突然の削除に対するアプリケーションの応答をテストすることができます。 

次の情報をお客様の情報に置き換えてください。 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```
`Response Code: 204` は、シミュレートされた削除が成功したことを意味します。 

**次の手順**

[Azure PowerShell](../windows/spot-powershell.md)、[ポータル](../spot-portal.md)、または[テンプレート](spot-template.md)を使用して Azure Spot Virtual Machine を作成することもできます。

Azure Spot Virtual Machine については、[Azure retail prices API](/rest/api/cost-management/retail-prices/azure-retail-prices) を使用して現在の価格情報を照会してください。 `meterName` と `skuName` の両方に `Spot` が含まれています。

エラーが発生した場合は、[エラー コード](../error-codes-spot.md)を参照してください。
