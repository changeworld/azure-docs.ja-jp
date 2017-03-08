---
title: "Azure Network Watcher の IP フロー検証を使用したトラフィックの検証 - Azure CLI | Microsoft Docs"
description: "この記事では、Azure CLI を使用して、仮想マシンから送受信されるトラフィックが許可されているか拒否されているかを確認する方法について説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: 3c0287783e3f2b7acaeefd87acbda30885f93c22
ms.lasthandoff: 02/23/2017


---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Azure Network Watcher の IP フロー検証コンポーネントを使用して VM から送受信されるトラフィックが許可されているか拒否されているかを確認する

> [!div class="op_single_selector"]
> - [Azure ポータル](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)

IP フロー検証は Network Watcher の機能であり、仮想マシンから送受信されるトラフィックが許可されているかどうかを検証できます。 このシナリオは、仮想マシンが現在外部リソースまたはバックエンドと通信可能な状態にあるかを確認する際に役立ちます。 IP フロー検証を使用すると、ネットワーク セキュリティ グループ (NSG) 規則が適切に構成されているかを検証し、NSG 規則によってブロックされているフローのトラブルシューティングを行うことができます。 ブロック対象のトラフィックが NSG により適切にブロックされているかどうかを確認することも、IP フロー検証を使用する別の理由として挙げられます。

## <a name="before-you-begin"></a>開始する前に

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関する手順を参照して Network Watcher を作成済みであるか、または既存の Network Watcher インスタンスがあることを前提としています。 また、有効な仮想マシンのあるリソース グループを使用することも前提としています。

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>シナリオ

このシナリオでは、IP フロー検証を使用して仮想マシンが既知の Bing IP アドレスと通信可能かどうかを検証します。 トラフィックが拒否されている場合、そのトラフィックを拒否するセキュリティ規則が返されます。 IP フロー検証の詳細については、[IP フロー検証の概要](network-watcher-ip-flow-verify-overview.md)に関する記事を参照してください。


## <a name="get-a-vm"></a>VM の取得

IP フロー検証では、仮想マシン上の IP アドレスとリモートの通信先間のトラフィックをテストします。 このコマンドレットには、仮想マシンの ID が必要です。 使用する仮想マシンの ID が既にわかっている場合、この手順は省略できます。

```
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="get-the-nics"></a>NIC の取得

仮想マシンの NIC の IP アドレスが必要になります。この例では、仮想マシン上の NIC を取得します。 仮想マシン上のテスト対象 IP アドレスを既にわかっている場合、この手順は省略できます。

```
azure network nic show -g resourceGroupName -n nicName
```

## <a name="run-ip-flow-verify"></a>IP フロー検証の実行

コマンドレットの実行に必要な情報が揃ったので、`network watcher ip-flow-verify` コマンドレットを実行してトラフィックをテストします。 この例では、1 番目の NIC の最初の IP アドレスを使用します。

```
azure network watcher ip-flow-verify -g resourceGroupName -n networkWatcherName -t targetResourceId -d directionInboundorOutbound -p protocolTCPorUDP -o localPort -m remotePort -l localIpAddr -r remoteIpAddr
```

> [!NOTE]
> IP フロー検証では、VM リソースを割り当てて実行する必要があります。

## <a name="review-results"></a>結果の確認

`network watcher ip-flow-verify` を実行すると結果が返されます。次の例に、前の手順で返された結果を示します。

```
data:    Access                          : Deny
data:    Rule Name                       : defaultSecurityRules/DefaultInboundDenyAll
info:    network watcher ip-flow-verify command OK
```

## <a name="next-steps"></a>次のステップ

ブロックされるべきでないトラフィックがブロックされている場合は、[ネットワーク セキュリティ グループの管理](../virtual-network/virtual-network-manage-nsg-arm-portal.md)に関する記事を参照して、ネットワーク セキュリティ グループと定義済みのセキュリティ規則を突き止めます。

NSG 設定の監査方法については、[Network Watcher を使用したネットワーク セキュリティ グループ (NSG) の監査](network-watcher-nsg-auditing-powershell.md)に関する記事をご覧ください。

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png

