---
title: VMware HCX Mobility Optimized Networking (MON) のガイダンス
description: Mobility Optimized Networking (MON) の Azure VMware Solution 固有のユース ケースについて説明します。
ms.topic: reference
ms.date: 10/04/2021
ms.openlocfilehash: 34840fedbf2ae2dbf32711f4dfee307fe3a3a5ca
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373329"
---
# <a name="vmware-hcx-mobility-optimized-networking-mon-guidance"></a>VMware HCX Mobility Optimized Networking (MON) のガイダンス

>[!NOTE]
>
>HCX Mobility Optimized Networking は VMware と Azure VMware Solution (HCX バージョン 4.1.0 以降) で公式サポートされています。 

>[!IMPORTANT] 
>
>HCX MON を有効にする前に、下の制限とサポートされていない構成をお読みください。
>
>[HCX NE のサポートされていないソース構成](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-DBDB4D1B-60B6-4D16-936B-4AC632606909.html)
> 
>[MON を含む HCX 展開の制限事項](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-BEC26054-D560-46D0-98B4-7FF09501F801.html)


[HCX Mobility Optimized Networking (MON)](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-0E254D74-60A9-479C-825D-F373C41F40BC.html) は、[HCX Network Extension (NE)](configure-hcx-network-extension.md) を使用する場合に有効にする、オプションの機能です。 MON を使用すると、特定のシナリオで最適なトラフィック ルーティングが提供され、拡張ネットワーク上のオンプレミスとクラウドベースのリソース間のネットワーク トロンボーンが回避されます。 

移行サイクル全体を通して、MON を使用することで次のアプリケーション モビリティが最適化されます。

- ストレッチ ネットワーク使用時の仮想マシン (VM) から VM L2 への通信の最適化 

- オンプレミス、Azure VMware Solution、Azure の間での非対称トラフィック フローの最適化と回避


この記事では、MON の Azure VMware Solution 固有のユース ケースについて説明します。


## <a name="optimize-traffic-flows-across-standard-and-stretched-segments-on-the-private-cloud-side"></a>プライベート クラウド側で標準とストレッチのセグメント全体のトラフィック フローを最適化する 

このシナリオでは、VM1 が NE を使用してクラウドに移行されます。これにより、VM から VM への最適な待機時間が提供されます。 その結果、VM1 からローカルの Azure VMware Solution セグメント上の VM3 までの低遅延が求められます。 トラフィックの最適なパス (青い線) を確保するために、VM1 ゲートウェイをオンプレミスから Azure VMware Solution (クラウド) に移行します。 ゲートウェイがオンプレミス (赤い線) のままである場合は、トロンボーン効果と待機時間の増加が観察されます。 

>[!NOTE]
>VM ゲートウェイをクラウド側に移行せずに MON を有効にしても、トラフィック フローの最適なパスは保証されません。  また、ポリシー ルートの評価もできません。

:::image type="content" source="media/tutorial-vmware-hcx/hcx-mon-user-case-diagram-1.png" alt-text="ストレッチ ネットワークを使用する場合の VM から VM L2 への通信の最適化を示す図。" border="false":::



## <a name="optimize-and-avoid-asymmetric-traffic-flows"></a>非対称トラフィック フローを最適化して回避する 

このシナリオでは、オンプレミスの VM が Azure VMware Solution に移行済みで L2 に参加し、L3 トラフィックのフローがオンプレミスに戻ってサービスにアクセスすると想定しています。 また、Azure からの一部の VM 通信が (Azure VMware Solution が接続された vNET で)、Azure VMware Solution プライベート クラウドに到達できると想定しています。

>[!IMPORTANT]
>ここでの要点は、非対称トラフィック フローを慎重に計画し、回避することです。 

既定で MON を使用しない場合、MON を使用しないストレッチ ネットワーク上の Azure VMware Solution 内の VM は、ExpressRoute 優先パスを使用してオンプレミスと通信できます。 理想的には、お客様のユース ケースに基づいて、MON が有効になっている Azure VMware Solution ストレッチ セグメント上の VM が、NE または ExpressRoute 経由の T0 ゲートウェイを介してオンプレミスに戻り、トラフィック フローを対称的に維持する方法を評価する必要があります。

たとえば、NE パスを選択する場合、MON ポリシー ルートは、オンプレミス側のサブネットを具体的にアドレス指定する必要があります。そうしないと、0.0/0 ルートが使用されます。 ポリシー ルートは、[詳細設定] を選択すると NE セグメントの下に表示されます。 既定では、すべての RFC1918 IP アドレスが MON ポリシー ルート定義に含まれます。 

:::image type="content" source="media/tutorial-vmware-hcx/default-hcx-mon-policy-based-routes.png" alt-text="既定のポリシーベースのルートを示すスクリーンショット。":::

ポリシー ルートは、VM ゲートウェイがクラウドに移行された場合にのみ評価されます。 この構成の効果は、宛先に一致するすべてのサブネットが NE アプライアンスを通してトンネリングされるという結果です。  一致しない場合は、T0 ゲートウェイ経由でルーティングされます。

>[!NOTE]
>Azure VMware Solution で MON を使用する際の特別な考慮事項は、BGP を使用してアドバタイズされた /32 ルートをピアに提供することです。これには、オンプレミスおよび ExpressRoute 接続を使用した Azure が含まれます。 たとえば、Azure 上の VM は、Azure VMware Solution MON が有効なセグメント上の Azure VMware Solution VM へのパスを学習します。 戻りトラフィックが予想どおりに T0 に送り返された後、リターン サブネットが RFC1918 と一致する場合、トラフィックは T0 ではなく NE を強制的に経由します。  その後、ExpressRoute を使用してオンプレミス側の Azure へのエグレスが行われます。  これにより、中間および非対称のルーティング動作でステートフル ファイアウォールの混乱が発生する可能性があります。 また、NE MON セグメント上の VM がどのような方法でインターネットにアクセスする必要があるか (Azure VMware Solution の T0 を介して、またはオンプレミスに戻る NE 経由でのみ) を決定することもおすすめします。

:::image type="content" source="media/tutorial-vmware-hcx/hcx-mon-user-case-diagram-3.png" alt-text="RFC1918 のエグレスとエグレス トラフィック フローを示す図。" border="false":::

上の図で説明したように、重要なのは、必要な各サブネットにポリシー ルートを一致させるすることです。 そうでないと、トラフィックは NE ではなく T0 を使用してルーティングされます。

 
ポリシー ルートの詳細については、「[Mobility Optimized Networking Policy Routes](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-F45B1DB5-C640-4A75-AEC5-45C58B1C9D63.html)」(Mobility Optimized Networking ポリシー ルート) を参照してください。

