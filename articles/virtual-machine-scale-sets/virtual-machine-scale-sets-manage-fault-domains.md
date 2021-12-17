---
title: Azure 仮想マシン スケール セットで障害ドメインを管理する
description: 仮想マシン スケール セットを作成するときに FD の適切な数を選択する方法について説明します。
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 12/18/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: ff513925647e5233afd6056677343dca88b8977b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697404"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>仮想マシン スケール セットに対する障害ドメインの適切な数を選択する
<<<<<<< HEAD
ゾーンのない Azure リージョンで仮想マシン スケール セットに対して既定で作成される障害ドメイン数の値は 5 です。 仮想マシン スケール セットのゾーン展開がサポートされるリージョンで、このオプションが選択されている場合、障害ドメイン数の既定値は各ゾーンで 1 となります。 この場合の FD = 1 とは、スケール セットに属する VM インスタンスがベスト エフォートで多くのラックに分散されることを意味します。
=======

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: ユニフォーム スケール セット

ゾーンのない Azure リージョンで仮想マシン スケール セットに対して既定で作成される障害ドメインの数は 5 個です。 仮想マシン スケール セットのゾーン展開がサポートされるリージョンで、このオプションが選択されている場合、障害ドメインの数の既定値はゾーンごとに 1 個です。 この場合の FD = 1 とは、スケール セットに属する VM インスタンスがベスト エフォートで多くのラックに分散されることを意味します。
>>>>>>> repo_sync_working_branch

スケール セットの障害ドメイン数を Managed Disks の障害ドメイン数と一致させることを検討することもできます。 これを一致させると、Managed Disks の障害ドメイン全体がダウンした場合に、クォーラムが失われるのを防ぐのに役立ちます。 FD の値は、各リージョンで使用可能な Managed Disks 障害ドメイン数以下に設定できます。 リージョンごとの Managed Disks 障害ドメイン数については、こちらの[ドキュメント](../virtual-machines/availability.md)をご覧ください。

## <a name="rest-api"></a>REST API
プロパティ `properties.platformFaultDomainCount` を、1、2、または 3 に設定できます (指定しない場合の既定値は 3)。 REST API のドキュメントについては、[こちら](/rest/api/compute/virtualmachinescalesets/createorupdate)をご覧ください。

## <a name="azure-cli"></a>Azure CLI
パラメーター `--platform-fault-domain-count` を、1、2、または 3 に設定できます (指定しない場合の既定値は 3)。 Azure CLI のドキュメントについては、[こちら](/cli/azure/vmss#az_vmss_create)をご覧ください。

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
- Azure 環境での[可用性と冗長性の機能](../virtual-machines/availability.md)についてさらに理解してください。
