---
title: "Azure Network Watcher Next Hop で次ホップを探す - Azure CLI | Microsoft Docs"
description: "この記事では、Next Hop で Azure CLI を使用して、次ホップの種類と IP アドレスを検索する方法について説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: 625618d200d1049b419128879a49f9e58f3a7627
ms.lasthandoff: 02/23/2017


---

# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli"></a>Azure Network Watcher の Next Hop 機能で Azure CLI を使用して次ホップの種類を検索する

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)


Next Hop は Network Watcher の機能の&1; つで、指定された仮想マシンに基づいて、次ホップの種類と IP アドレスを取得します。 この機能は、仮想マシンからのトラフィックがゲートウェイ、インターネット、または仮想ネットワークを経由して宛先に到達するかどうかを判断する際に役立ちます。

## <a name="before-you-begin"></a>開始する前に

このシナリオでは Azure CLI を使用して、次ホップの種類と IP アドレスを検索します。

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関するページの手順を参照して、Network Watcher を作成済みであることを前提としています。 また、有効な仮想マシンのあるリソース グループを使用することも前提としています。

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>シナリオ

この記事で説明するシナリオでは、リソースの次ホップの種類と IP アドレスを探す、Next Hop という Network Watcher の機能を使用します。 Next Hop の詳細については、[Next Hop の概要](network-watcher-next-hop-overview.md)に関するページをご覧ください。


## <a name="get-next-hop"></a>次ホップの取得

次ホップを取得するには、`azure netowrk watcher next-hop` コマンドレットを呼び出します。 このコマンドレットには、Network Watcher リソース グループ、Network Watcher、仮想マシン ID、送信元 IP アドレス、宛先 IP アドレスを渡します。 この例では、宛先 IP アドレスは別の仮想ネットワークにある VM です。 2 つの仮想ネットワークの間には、仮想ネットワーク ゲートウェイがあります。

```azurecli
azure network watcher next-hop -g resourceGroupName -n networkWatcherName -t targetResourceId -a <source-ip> -d <destination-ip>
```

> [!NOTE]
VM に複数の NIC があり、NIC のいずれかで IP 転送が有効な場合は、NIC のパラメーター (-i nic-id) を指定する必要があります。 それ以外の場合は省略可能です。

## <a name="review-results"></a>結果の確認

完了すると、結果が表示されます。 次ホップの IP アドレスと、リソースの種類が返されます。

```
data:    Next Hop Ip Address             : 10.0.1.2
info:    network watcher next-hop command OK
```

現在利用可能な NextHopType の値は次のとおりです。

**次ホップの種類**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

## <a name="next-steps"></a>次のステップ

[Network Watcher での NSG 監査](network-watcher-nsg-auditing-powershell.md)に関するページから、プログラムを使用してネットワーク セキュリティ グループの設定を確認する方法を見る

