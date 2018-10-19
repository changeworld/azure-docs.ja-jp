---
title: Azure Network Watcher の次ホップの概要 | Microsoft Docs
description: この記事では、Network Watcher の次ホップ機能の概要を説明します。
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 28eacdce922e26d391cf34f78cb03ead9c6887a1
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391268"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>次ホップを使用して仮想マシンのルーティングの問題を診断する

仮想マシン (VM) からのトラフィックは、ネットワーク インターフェイス (NIC) に関連付けられた有効なルートをもとに、送信先に送信されます。 次ホップは、特定の VM と NIC から次ホップの種類とパケットの IP アドレスを取得します。 次ホップを知ると、トラフィックが目的の宛先に転送されているかどうか、またはトラフィックがどこにも送信されていないかどうかを判断できます。 不適切なルートを構成して、トラフィックがオンプレミスの場所または仮想アプライアンスに向けられると、接続に関する問題が起こります。 また、次ホップは関連するルート テーブルを返します。 ルートがユーザー定義のルートとして定義されている場合、そのルートが返されます。 それ以外の場合、次ホップは**システム ルート**を返します。

![次ホップの概要](./media/network-watcher-next-hop-overview/figure1.png)

次ホップ機能から返されることを次ホップは次のとおりです。

* インターネット
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* なし

各次ホップの種類の詳細については、[ルーティングの概要](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

次ホップを使用して VM ネットワークのルーティングの問題を診断する方法については、[Azure Portal を使用した仮想マシン ネットワークのルーティングの問題の診断](diagnose-vm-network-routing-problem.md)に関するページを参照してください。
