---
title: ExpressRoute 経由の BFD の構成 | Microsoft Docs
description: このドキュメントでは、ExpressRoute 回線のプライベート ピアリング経由で BFD を構成する方法について説明します。
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 8/17/2018
ms.author: rambala
ms.openlocfilehash: 97ac49ff5cc50621fd902f77c2543a428c7e8bae
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49384754"
---
# <a name="configure-bfd-over-expressroute"></a>ExpressRoute 経由の BFD の構成

ExpressRoute では、プライベート ピアリング経由の Bidirectional Forwarding Detection (BFD) をサポートしています。 ExpressRoute 経由の BFD を有効にすると、Microsoft Enterprise Edge (MSEE) デバイスと ExpressRoute 回線が終了するルーター (PE) の間のリンク障害の検出を迅速化できます。 顧客のエッジ ルーティング デバイスまたはパートナーのエッジ ルーティング デバイス経由の ExpressRoute を終了できます (マネージド レイヤー 3 接続サービスを選択した場合)。 このドキュメントでは、BFD の必要性と、ExpressRoute 経由の BFD を有効にする方法について説明します。

## <a name="need-for-bfd"></a>BFD の必要性

次の図は、ExpressRoute 回線経由の BFD を有効にする利点を示しています。[![1]][1]

ExpressRoute 回線は、レイヤー 2 接続またはマネージド レイヤー 3 接続のいずれかで有効にできます。 いずれの場合も、ExpressRoute 接続パスに 1 つ以上のレイヤー 2 デバイスが存在するときは、上にある BGP に、パスのリンク障害を検出する責任があります。

MSEE デバイスでは、通常、BGP のキープアライブとホールド時間はそれぞれ 60 秒と 180 秒に設定されています。 したがって、リンク障害が発生してから、リンク障害が検出され、トラフィックが別の接続に切り替わるまでの時間は最大で 3 分です。

BGP タイマーを制御するには、顧客のエッジ ピアリング デバイスで BGP のキープアライブとホールド時間を低く設定します。 2 つのピアリング デバイス間で BGP タイマーが一致しない場合、ピア間の BGP セッションでは、低い方のタイマー値が使用されます。 設定できる BGP キープアライブの最短時間は 3 秒で、ホールド時間は数十秒です。 ただし、プロトコルで大量のプロセスが集中的に消費されるため、BGP タイマーを強引に低く設定することはお勧めしません。

このシナリオでは、BFD が役立ちます。 BFD では、1 秒未満の時間間隔で低オーバーヘッドのリンク障害検出が可能です。 


## <a name="enabling-bfd"></a>BFD の有効化

MSEE では、新しく作成されたすべての ExpressRoute プライベート ピアリング インターフェイスで、BFD が既定で構成されています。 したがって、BFD を有効にするには、お使いの PE で BFD を構成するだけです。 BFD は 2 段階のプロセスで構成します。つまり、最初にインターフェイス上で構成し、次に、それを BGP セッションにリンクします。

(Cisco IOS XE を使用した) PE 構成の例を次に示します。 

    interface TenGigabitEthernet2/0/0.150
      description private peering to Azure
      encapsulation dot1Q 15 second-dot1q 150
      ip vrf forwarding 15
      ip address 192.168.15.17 255.255.255.252
      bfd interval 300 min_rx 300 multiplier 3


    router bgp 65020
      address-family ipv4 vrf 15
        network 10.1.15.0 mask 255.255.255.128
        neighbor 192.168.15.18 remote-as 12076
        neighbor 192.168.15.18 fall-over bfd
        neighbor 192.168.15.18 activate
        neighbor 192.168.15.18 soft-reconfiguration inbound
      exit-address-family

>[!NOTE]
>既にある既存のプライベート ピアリングで BFD を有効にするには、そのピアリングをリセットする必要があります。 「[Reset ExpressRoute peerings (ExpressRoute ピアリングのリセット)][ResetPeering]」を参照してください
>

## <a name="bfd-timer-negotiation"></a>BFD タイマー ネゴシエーション

BFD ピア間では、2 つのピアのうち遅い方のピアによって送信レートが決まります。 MSEE の BFD 送信/受信間隔は、300 ミリ秒に設定されています。 大きな値を設定すると、これらの間隔を強制的に大きくできますが、短くすることはできません。

>[!NOTE]
>geo 冗長 ExpressRoute プライベート ピアリング回線を構成した場合、またはサイト間 IPSec VPN 接続を ExpressRoute プライベート ピアリング用のバックアップとして使用する場合は、プライベート ピアリング経由の BFD を有効にすると、ExpressRoute 接続エラー後のフェールオーバーをより迅速に行うことができます。 
>

## <a name="next-steps"></a>次の手順

詳細やヘルプについては、次のリンク先を確認してください。

- [ExpressRoute 回線の作成と変更][CreateCircuit]
- [ExpressRoute 回線のルーティングの作成と変更を行う][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD によりリンク障害で差し引かれる時間が短縮"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






