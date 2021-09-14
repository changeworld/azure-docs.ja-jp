---
title: 'Azure Route Server を使用したデュアルホーム ネットワークについて '
description: Azure Route Server がデュアルホーム ネットワークでどのように機能するかについて説明します。
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: duau
ms.openlocfilehash: 76c1a1e424e4f8ae372f53ceab5da43ca9d40629
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434576"
---
# <a name="about-dual-homed-network-with-azure-route-server"></a>Azure Route Server を使用したデュアルホーム ネットワークについて

Azure Route Server によって、一般的なハブとスポークのネットワーク トポロジがサポートされています。 この構成は、ルート サーバーとネットワーク仮想アプライアンス (NVA) の両方がハブ仮想ネットワーク内にある場合のものです。 Router Server を使用すると、デュアルホーム ネットワークと呼ばれる別のトポロジを構成することもできます。 この構成は、2 つ以上のハブ仮想ネットワークとピアリングされたスポーク仮想ネットワークがある場合のものです。 スポーク仮想ネットワーク内の仮想マシンは、オンプレミスまたはインターネットへのハブ仮想ネットワークを経由して通信できます。

## <a name="how-to-set-it-up"></a>設定方法

次の図に示すように、次のことを行う必要があります。

* 各ハブ仮想ネットワークに NVA を、スポーク仮想ネットワークにルート サーバーをデプロイします。
* ハブおよびスポークの仮想ネットワーク間で VNet ピアリングを有効にします。
* ルート サーバーとデプロイされた各 NVA との間の BGP ピアリングを構成します。

:::image type="content" source="./media/about-dual-homed-network/dual-homed-topology.png" alt-text="デュアルホーム トポロジのルート サーバーの図。":::

### <a name="how-does-it-work"></a>それはどのように機能しますか?

コントロール プレーンで、NVA とルート サーバーは、同じ仮想ネットワークにデプロイされているかのように、ルートを交換します。 NVA は、ルート サーバーからスポーク仮想ネットワーク アドレスについて学習します。 ルート サーバーは、各 NVA からのルートを学習します。 その後、ルート サーバーで学習したルートを使用して、スポーク仮想ネットワーク内のすべての仮想マシンがプログラムされます。 

データ プレーンで、スポーク仮想ネットワーク内の仮想マシンによって、ハブ内のセキュリティ NVA または VPN NVA が次ホップとして認識されます。 インターネットにバインドされたトラフィックまたはハイブリッド クロス プレミス トラフィック宛てのトラフィックは、ハブ仮想ネットワーク内の NVA を経由してルーティングされるようになります。 両方のハブをアクティブ/アクティブまたはアクティブ/パッシブのどちらかに構成できます。 アクティブなハブで障害が発生した場合、仮想マシンとの間のトラフィックはもう一方のハブにフェールオーバーされます。 これらのエラーには、NVA エラーやサービス接続エラーが含まれますが、これらに限定されません。 この設定により、ネットワークが高可用性用に構成されます。

## <a name="integration-with-expressroute"></a>ExpressRoute との統合

2 つ以上の ExpressRoute 接続を含むデュアルホーム ネットワークを構築できます。 上記の手順と共に、次のことを実行する必要があります。

* ExpressRoute ゲートウェイを持つ各ハブ仮想ネットワークに別のルート サーバーを作成します。
* ハブ仮想ネットワーク内の NVA とルート サーバーの間に BGP ピアリングを構成します。
* ハブ仮想ネットワーク内の ExpressRoute ゲートウェイとルート サーバーとの間の[ルート交換を有効に](quickstart-configure-route-server-portal.md#configure-route-exchange)します。
* スポーク仮想ネットワークの VNet ピアリング構成で "リモート ゲートウェイまたはリモート ルート サーバーを使用する" ことが **無効** になっている必要があります。

:::image type="content" source="./media/about-dual-homed-network/dual-homed-topology-expressroute.png" alt-text="ExpressRoute を使用したデュアルホーム トポロジのルート サーバーの図。":::

### <a name="how-does-it-work"></a>それはどのように機能しますか?

コントロール プレーンで、ハブ仮想ネットワーク内の NVA は、ExpressRoute ゲートウェイからハブ内のルート サーバーとの[ルート交換](quickstart-configure-route-server-portal.md#configure-route-exchange)を介してオンプレミスのルートについて学習します。 同じルート サーバーを使用して、今度は NVA から ExpressRoute ゲートウェイにスポーク仮想ネットワーク アドレスが送信されます。 スポークとハブ両方の仮想ネットワークのルート サーバーで、それぞれの仮想ネットワーク内の仮想マシンへのオンプレミス ネットワーク アドレスがプログラムされます。

> [!IMPORTANT]
> BGP で AS パスの AS 番号を確認することによってループが回避されます。 受信ルーターで受信された BGP パケットの AS パスに独自の AS 番号が確認された場合、パケットがドロップされます。 この例では、両方のルート サーバーの AS 番号が同じ 65515 です。 各ルート サーバーで他のルート サーバーからのルートが削除されるのを防ぐには、NVA と各ルート サーバーのピアリング時に、**as-override** の BGP ポリシーを適用する必要があります。 
>

データ プレーンで、スポーク仮想ネットワーク内の仮想マシンから、オンプレミス ネットワーク宛てのすべてのトラフィックがハブ仮想ネットワーク内の NVA に最初に送信されます。 その後、NVA から ExpressRoute 経由でオンプレミス ネットワークにトラフィックが転送されます。 オンプレミスからのトラフィックは、同じデータ パスを逆方向に進みます。 どちらのルート サーバーもデータ パスに存在しないことがわかります。

## <a name="next-steps"></a>次のステップ

* [Azure Route Server が ExpressRoute とどのように連携するかについて学習する](expressroute-vpn-support.md)
* [Azure Route Server がネットワーク仮想アプライアンスとどのように連携するかについて学習する](resource-manager-template-samples.md)

