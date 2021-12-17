---
title: 仮想ハブとの BGP ピアリングについて
titleSuffix: Azure Virtual WAN
description: Azure Virtual WAN 仮想ハブとの BGP ピアリングについて説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: cherylmc
ms.openlocfilehash: 5cc5b3dd26d0cb88460fabbd2ceb3cd28b107121
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129075"
---
# <a name="scenario-bgp-peering-with-a-virtual-hub-preview"></a>シナリオ: 仮想ハブとの BGP ピアリング (プレビュー)

> [!IMPORTANT]
> Virtual WAN ハブを使用した BGP ピアリング機能は、現在、限定パブリック プレビュー段階にあります。 この機能を試したい場合は、 **previewbgpwithvhub@microsoft.com** まで、Virtual WAN リソースのリソース ID と共に電子メールを送信してください。 機能が有効化されたことを示す通知を受け取ったら、主な考慮事項と詳細な構成手順について、以下の文書の[ページ](create-bgp-peering-hub-portal.md)を確認してください。 
>
> リソース ID を見つけるには、Azure portal を開いて Virtual WAN リソースにナビゲートし、 **[設定] > [プロパティ] > [リソース ID]** をクリックします。<br> 例: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>`
>


Azure Virtual WAN ハブ ルーター (仮想ハブ ルーターとも呼ばれます) は、ルート マネージャーとして機能し、仮想ハブ内および仮想ハブ間のルーティング操作を簡略化します。 つまり、仮想ハブ ルーターには以下の機能があります。

* VPN、ExpressRoute、P2S、ネットワーク仮想アプライアンス (NVA) などのゲートウェイと通信する中央ルーティング エンジンになり、ルーティング管理を簡素化します。 
* カスタム ルート テーブル、関連付け、およびルートの伝達の高度なルーティング シナリオを有効にします。
* 仮想ハブに接続されている仮想ネットワークに転送されるトラフィック、またはそれらの仮想ネットワーク間で転送されるトラフィックのルーターとして機能します。

現在、仮想ハブ ルーターでは、それとのピアリング機能も公開されたため、Border Gateway Protocol (BGP) ルーティング プロトコルを介してルーティング情報が直接交換されるようになりました。 仮想ハブに接続されている仮想ネットワークにプロビジョニングされた NVA または BGP エンド ポイントは、仮想ハブ ルーターと直接ピアリングできます。ただし、BGP ルーティング プロトコルをサポートし、NVA 上の ASN が仮想ハブの ASN と異なるように設定されている必要があります。

## <a name="benefits-and-considerations"></a>利点と考慮事項

**主な利点**

* 仮想ネットワーク アドレスが更新されるたびに、NVA のルーティング テーブルを手動で更新する必要がなくなりました。
* NVA により新しいルートがアナウンスされたり、古いルートが取り消されたりするたびに、ユーザー定義のルートを手動で更新する必要がなくなりました。
* 仮想ハブに接続されている仮想ネットワーク内の NVA は、仮想ハブ ゲートウェイ (VPN、ExpressRoute、またはマネージド NVA) ルートを学習できます。
* NVA の複数のインスタンスを仮想ハブ ルーターにピアリングできます。 NVA で BGP 属性を構成し、設計 (アクティブ/アクティブまたはアクティブ/パッシブ) に応じて、どの NVA インスタンスがアクティブまたはパッシブであるかを仮想ハブ ルーターに知らせることができます。

**考慮事項**

* 仮想ハブ ルーターを、仮想ネットワークにプロビジョニングされた Azure Route Server とピアリングすることはできません。
* 仮想ハブ ルーターでは、16 ビット (2 バイト) の ASN のみがサポートされています。
* NVA BGP 接続エンドポイントがある仮想ネットワーク接続は、常に defaultRouteTable に関連付け、伝達する必要があります。 現時点では、カスタム ルート テーブルはサポートされていません。
* 仮想ハブ ルーターでは、仮想ハブに接続されている仮想ネットワーク間の転送接続がサポートされています。 Virtual WAN では既に転送接続がサポートされているため、このことは、この BGP ピアリング機能とは関係ありません。 例:
  * VNET1: 仮想ハブ 1 に接続されている NVA1 -> (転送接続) -> VNET2: 仮想ハブ 1 に接続されている NVA2。
  * VNET1: 仮想ハブ 1 に接続されている NVA1 -> (転送接続) -> VNET2: 仮想ハブ 2 に接続されている NVA2。
* ネットワーク仮想アプライアンスで、独自のパブリック ASN またはプライベート ASN を使用できます。 Azure または IANA によって予約されている範囲を使用することはできません。 次の ASN は、Azure または IANA によって予約されています。
   * Azure によって予約済みの ASN:
     * パブリック ASN: 8074、8075、12076
     * プライベート ASN: 65515、65517、65518、65519、65520
   * IANA によって予約済みの ASN: 23456、64496-64511、65535-65551
* 仮想ハブ ルーターは BGP ルートを NVA と交換し、それらを仮想ネットワークに伝達しますが、同時に、仮想ハブでホストされるゲートウェイ (VPN Gateway、ExpressRoute ゲートウェイ、マネージド NVA ゲートウェイ) を介してオンプレミスからのルートの伝達を直接行います。 

   仮想ハブ ルーターには、以下の制限があります。

   | リソース | 制限 |
   |---|---|
   |  各 BGP ピアで仮想ハブにアドバタイズできるルートの数。| ハブは、接続されているリソースから、最大で 10,000 のルート (合計) のみを受け入れることができます。 たとえば仮想ハブに、接続された仮想ネットワーク、ブランチ、仮想ハブなどからの合計で 6,000 のルートがある場合、新しい BGP ピアリングが NVA で構成されると、NVA は最大で 4,000 のルートのみをアドバタイズできます。 |
* 仮想ネットワークのアドレス空間より具体的な仮想ネットワーク内の NVA からのルートは、BGP 経由で仮想ハブに公開されるときは、それ以上オンプレミスに伝達されません。
* 仮想ハブに直接接続されている仮想ネットワーク内のアドレス宛てのトラフィックは、ハブと NVA の間の BGP ピアリングを使用して NVA 経由でルーティングするように構成することはできません。 これは、スポークの仮想ネットワーク接続が作成されるときに、スポーク仮想ネットワークのアドレスに関連付けられているシステム ルートが、仮想ハブによって自動的に学習されるためです。 これらの自動学習されるシステム ルートは、BGP を介してハブによって学習されたルートより優先されます。

## <a name="bgp-peering-scenarios"></a>BGP ピアリングのシナリオ

このセクションでは、BGP ピアリング機能を使用してルーティングを構成できるシナリオについて説明します。

## <a name="transit-vnet-connectivity"></a><a name="vnet-vnet"></a>転送 VNet 接続

:::image type="content" source="./media/scenario-bgp-peering-hub/vnet-vnet.png" alt-text="VNet 対 VNet ルーティングの図。":::

このシナリオでは、"Hub 1" という名前の仮想ハブがいくつかの仮想ネットワークに接続されています。 目標は、仮想ネットワーク VNET1 と VNET5 間のルーティングを確立することです。

### <a name="configuration-steps-without-bgp-peering"></a>BGP ピアリングを使用しない構成手順

仮想ハブで BGP ピアリングを使用しない場合は、以下の手順が必要です。

仮想ハブの構成

* Hub 1 の defaultRouteTable で、VNET2 接続を指す VNET5 (サブネット 10.2.1.0/24) の静的ルートを構成します。
* Hub 1 の VNET2 用の仮想ネットワーク接続で、VNET2 NVA IP (サブネット 10.2.0.5) を指す VNET5 の静的ルートを構成します。
* Hub 1 で、VNET1 と VNET2 の接続からのルートを defaultRouteTable に伝達し、それらを defaultRouteTable に関連付けます。

Virtual Network の構成

* VNET5 で、VNET2 NVA IP を指すユーザー定義ルート (UDR) を設定します。

### <a name="configuration-steps-with-bgp-peering"></a>BGP ピアリングを使用する構成手順

前の構成では、VNET5 の構成が頻繁に変更される場合、静的ルートと UDR のメンテナンスが複雑になることがあります。 この課題に対処するには、仮想ハブとの BGP ピアリング機能を使用することができ、ルーティング構成を以下の手順で変更する必要があります。

仮想ハブの構成

* Hub 1 で、VNET2 NVA を BGP ピアとして構成します。 また、VNET2 NVA を、Hub 1 との BGP ピアリングを行うように構成します。
* Hub 1 で、VNET1 と VNET2 の接続からのルートを defaultRouteTable に伝達し、それらを defaultRouteTable に関連付けます。

Virtual Network の構成

* VNET5 で、VNET2 NVA IP を指すユーザー定義ルート (UDR) を設定します。

#### <a name="effective-routes"></a>有効なルート

次の表は、defaultRouteTable にある Hub 1 の有効なルートのうち、いくつかのエントリを示しています。 VNET5 (サブネット 10.2.1.0/24) のルートに注意してください。これは、VNET1 と VNET5 が互いに通信できることを示しています。

| 宛先プレフィックス |  次ホップ| 出発地 | ASN パス|
| --- | --- | --- | ---|
| 10.2.0.0/24 |eastusconn  | VNet 接続 ID | - |
| 10.2.1.0/24 |NVA の BGP ピア接続 ID  | NVA の BGP ピア接続 ID |  65510|
|  10.4.1.0/24 | Hub 2 | Hub 2 | - |

この機能を使用してこの方法でルーティングを構成すると、仮想ハブで静的ルート エントリが不要になります。 そのため、構成がより簡単になり、接続済みの仮想ネットワーク (VNET5 など) の構成が変更されるとルート テーブルが動的に更新されます。

## <a name="branch-vnet-connectivity"></a><a name="branch-vnet"></a>ブランチ VNet 接続

:::image type="content" source="./media/scenario-bgp-peering-hub/branch-vnet.png" alt-text="ブランチから VNet へのルーティングの図。":::

このシナリオでは、"NVA Branch 1" という名前のオンプレミス サイトに、VNET2 NVA を終端とするように構成された VPN があります。 目標は、NVA Branch 1 と仮想ネットワーク VNET1 の間のルーティングを構成することです。

### <a name="configuration-steps-without-bgp-peering"></a>BGP ピアリングを使用しない構成手順

仮想ハブで BGP ピアリングを使用しない場合は、以下の手順が必要です。

仮想ハブの構成

* Hub 1 の defaultRouteTable で、VNET2 接続を指す NVA Branch 1 の静的ルートを構成します。
* Hub 1 の VNET2 用の仮想ネットワーク接続で、VNET2 NVA IP (10.2.0.5) を指す NVA Branch 1 の静的ルートを構成します。
* Hub 1 で、VNET1 と VNET2 の接続からのルートを defaultRouteTable に伝達し、それらを defaultRouteTable に関連付けます。

Virtual Network の構成

* VNET2 NVA と NVA Branch 1 の間の BGP ピアリングと、VNET2 NVA から NVA Branch 1 への VNET1 のルート アドバタイズ。

### <a name="configuration-steps-with-bgp-peering"></a>BGP ピアリングを使用する構成手順

時間が経過すると、NVA Branch 1 の宛先プレフィックスが変わる場合があります。また、NVA Branch 1 のような、VNET1 への接続を必要とする多くのサイトがある場合もあります。 そのため、Hub 1 と VNET2 接続の静的ルートを更新する必要が生じ、煩雑になる場合があります。 このような場合は、仮想ハブとの BGP ピアリング機能を使用できます。ルーティング接続の構成手順は次のようになります。

仮想ハブの構成

* Hub 1 で、VNET2 NVA を BGP ピアとして構成します。 また、VNET2 NVA を、Hub 1 との BGP ピアリングを行うように構成します。
* Hub 1 で、VNET1 と VNET2 の接続からのルートを defaultRouteTable に伝達し、それらを defaultRouteTable に関連付けます。

Virtual Network の構成

* VNET2 NVA と NVA Branch 1 の間の BGP ピアリングと、VNET2 NVA から NVA Branch 1 への VNET1 のルート アドバタイズ。

#### <a name="effective-routes"></a>有効なルート

次の表は、defaultRouteTable にある Hub 1 の有効なルートのうち、いくつかのエントリを示しています。 NVA Branch 1 (サブネット 192.168.1.0/24) のルートが、NVA との BGP ピアリングを通じて学習されることに注意してください。

 | 宛先プレフィックス |  次ホップ| 出発地 | ASN パス|
| --- | --- | --- | ---|
| 10.2.0.0/24 | eastusconn  | VNet 接続 ID | - |
| 192.168.1.0/24 | NVA の BGP ピア接続 ID  | NVA の BGP ピア接続 ID |  65510|

NVA Branch 1 でのネットワークの変更を管理したり、NVA Branch 1 のような新しいサイト間の接続を確立したりするために、Hub 1 で追加の構成を行う必要はありません。これは、Hub 1 と NVA の間の BGP ピアリングによってルート テーブルが動的に更新されるためです。 そのため、構成とメンテナンスが簡略化されます。

## <a name="next-steps"></a>次のステップ

* [仮想ハブとの BGP ピアリングを構成する](create-bgp-peering-hub-portal.md)。
