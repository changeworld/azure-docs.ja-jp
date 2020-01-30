---
title: Azure 仮想マシン スケール セットで障害ドメインを管理する
description: 仮想マシン スケール セットを作成するときに FD の適切な数を選択する方法について説明します。
author: rajsqr
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: 297837354cea4bb5ccdcc03261810dcffd144243
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275722"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>仮想マシン スケール セットに対する障害ドメインの適切な数を選択する
ゾーンのない Azure リージョンで仮想マシン スケール セットに対して既定で作成される障害ドメインの数は 5 個です。 仮想マシン スケール セットのゾーン展開がサポートされるリージョンで、このオプションが選択されている場合、障害ドメインの数の既定値はゾーンごとに 1 個です。 この場合の FD = 1 とは、スケール セットに属する VM インスタンスがベスト エフォートで多くのラックに分散されることを意味します。

スケール セットの障害ドメインの数を Managed Disks の障害ドメインの数と一致させることを検討することもできます。 これを一致させると、Managed Disks の障害ドメイン全体がダウンした場合に、クォーラムが失われるのを防ぐのに役立ちます。 FD の数は、各リージョンで使用可能な Managed Disks 障害ドメインの数以下に設定できます。 リージョンごとの Managed Disks 障害ドメインの数については、こちらの[ドキュメント](../virtual-machines/windows/manage-availability.md)をご覧ください。

## <a name="rest-api"></a>REST API
プロパティ `properties.platformFaultDomainCount` を、1、2、または 3 に設定できます (指定しない場合の既定値は 5)。 REST API のドキュメントについては、[こちら](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)をご覧ください。

## <a name="azure-cli"></a>Azure CLI
パラメーター `--platform-fault-domain-count` を、1、2、または 3 に設定できます (指定しない場合の既定値は 5)。 Azure CLI のドキュメントについては、[こちら](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)をご覧ください。

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。

## <a name="next-steps"></a>次のステップ
- Azure 環境での[可用性と冗長性の機能](../virtual-machines/windows/availability.md)についてさらに理解してください。
