---
title: CLI を使用して Azure VM のメンテナンス通知を取得する
description: Azure で実行されている仮想マシンのメンテナンス通知を表示し、Azure CLI を使用してセルフサービス メンテナンスを開始します。
services: virtual-machines
author: shants123
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 3c75adc2bf5974c1bce9f05306342068396ae62b
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534939"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Azure CLI に対する計画済みメンテナンスの通知の処理

**この記事は、Linux を実行する仮想マシンと Windows を実行する仮想マシンの両方に適用されます。**

Azure CLI を使用して、VM の[メンテナンス](maintenance-notifications.md)の予定を確認できます。 計画メンテナンスの情報は、[az vm get-instance-view](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view) から確認できます。
 
計画メンテナンスがある場合にのみ、メンテナンス情報が返されます。 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>メンテナンスを開始する

`IsCustomerInitiatedMaintenanceAllowed` が true に設定されている場合、次の呼び出しによって VM に対するメンテナンスが開始されます。

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>クラシック デプロイ


クラシック デプロイ モデルを使用してデプロイされたレガシ VM がまだある場合は、Azure クラシック CLI を使用して、VM を照会し、メンテナンスを開始できます。

次のように入力して、クラッシック VM を操作する正しいモードになっていることを確認します。

```
azure config mode asm
```

*myVM* という名前の VM のメンテナンスの状態を取得するには、次のように入力します。

```
azure vm show myVM 
``` 

*myService* サービスおよび *myDeployment* デプロイの *myVM* という名前のクラシック VM でメンテナンスを開始するには、次のように入力します。

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>次の手順

また、[Azure PowerShell](maintenance-notifications-powershell.md) または [portal](maintenance-notifications-portal.md) を使用して、計画メンテナンスを扱うこともできます。
