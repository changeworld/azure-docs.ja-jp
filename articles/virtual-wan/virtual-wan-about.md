---
title: Azure Virtual WAN の概要 | Microsoft Docs
description: Virtual WAN の自動化されたスケーラブルな支店間接続について学習します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/25/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: c2edb821eb8bd9a5da7a6cce81269e7d3f611722
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869893"
---
# <a name="what-is-azure-virtual-wan"></a>Azure Virtual WAN とは

Azure Virtual WAN は、Azure を介して支店間接続を最適化し、自動化するネットワーク サービスです。 Virtual WAN を使用すると、ブランチ デバイスを接続および構成して Azure と通信できます。 これは、手動で行うことも、Virtual WAN パートナーを介して推奨パートナー デバイスを使用して行うこともできます。 [推奨パートナー](https://go.microsoft.com/fwlink/p/?linkid=2019615)に関するページを参照してください。 推奨パートナー デバイスを使用すると、使いやすさ、接続の簡素化、および構成管理を実現できます。 Azure WAN の組み込みダッシュボードにはすぐに使用できるトラブルシューティング用分析情報が用意されているため、時間を節約でき、大規模な接続を簡単に表示することができます。

![Virtual WAN のダイアグラム](./media/virtual-wan-about/virtualwan.png)

この記事では、Azure と Azure 以外のワークロードのネットワーク接続を簡単に説明します。 Virtual WAN には次の利点があります。

* **ハブとスポーク型の統合された接続ソリューション:** オンプレミスのサイトと Azure ハブとの間のサイト間の構成と接続を自動化します。
* **スポークの設定と構成の自動化:** 仮想ネットワークとワークロードを Azure ハブにシームレスに接続します。
* **直感的なトラブルシューティング:** Azure 内でエンドツーエンドのフローを確認し、この情報を使用して必要なアクションを実行します。

## <a name="s2s"></a>サイト間接続

Virtual WAN を使用してサイト間接続を作成するには、[Virtual WAN パートナー](virtual-wan-locations-partners.md)を利用するか、手動で接続を作成します。

### <a name="s2spartner"></a>Virtual WAN パートナーとの連携

Virtual WAN パートナーと連携する場合のプロセスは、次のとおりです。

1. ブランチ デバイス (VPN/SDWAN) コントローラーが、[Azure サービス プリンシパル](../active-directory/develop/howto-create-service-principal-portal.md)を使用してサイト中心の情報を Azure にエクスポートするために認証を受けます。
2. ブランチ デバイス (VPN/SDWAN) コントローラーが、Azure 接続構成を取得し、ローカル デバイスを更新します。 これにより、オンプレミス VPN デバイスの構成のダウンロード、編集、および更新が自動化されます。
3. デバイスに適切な Azure 構成が設定されると、Azure WAN に対してサイト間接続 (2 つのアクティブなトンネル) が確立されます。 Azure では、IKEv1 と IKEv2 の両方がサポートされています。 BGP はオプションです。


推奨パートナーを使用しない場合は、接続を手動で構成することができます。[Virtual WAN を使用したサイト間接続の作成](virtual-wan-site-to-site-portal.md)に関するページを参照してください。

## <a name="p2s"></a>ポイント対サイト接続 (プレビュー)

ポイント対サイト (P2S) 接続では、個々のクライアント コンピューターから仮想ハブへの、セキュリティで保護された接続を作成することができます。 P2S 接続は、クライアント コンピューターから接続を開始することによって確立されます。 このソリューションは、在宅勤務の人間が自宅や会議室など、リモートの場所から接続する場合に便利です。 P2S VPN は、接続する必要があるクライアントが数台のみの場合に、S2S VPN の代わりに使用するソリューションとしても便利です。

手動で接続を作成するには、[Virtual WAN を使用したポイント対サイト接続の作成](virtual-wan-point-to-site-portal.md)に関するページを参照してください。

## <a name="er"></a>ExpressRoute 接続 (プレビュー)

手動で接続を作成するには、[Virtual WAN を使用した ExpressRoute 接続の作成](virtual-wan-expressroute-portal.md)に関するページを参照してください。


## <a name="resources"></a>Virtual WAN リソース

エンドツーエンドの仮想 WAN を構成するには、次のリソースを作成します。

* **virtualWAN:** virtualWAN リソースは、Azure ネットワークの仮想オーバーレイを表し、複数のリソースのコレクションです。 これには、仮想 WAN 内に配置するすべての仮想ハブへのリンクが含まれます。 Virtual WAN リソースは相互に分離されており、共通のハブを含むことはできません。 Virtual WAN 上の仮想ハブは互いに通信しません。 ‘ブランチ間のトラフィックを許可する’ プロパティにより、VPN サイト間と VPN 対 ExpressRoute 対応サイト間のトラフィックが有効になります。 Azure Virtual WAN の ExpressRoute は、現在はプレビュー段階です。

* **サイト:** vpnsite と呼ばれるサイト リソースは、オンプレミスの VPN デバイスとその設定を表します。 Virtual WAN パートナーと連携することで、この情報を Azure に自動的にエクスポートする組み込みのソリューションが得られます。

* **ハブ:** 仮想ハブは、Microsoft が管理する仮想ネットワークです。 ハブには、オンプレミス ネットワーク (vpnsite) からの接続を可能にするさまざまなサービス エンドポイントが含まれています。 ハブは、リージョン内のネットワークのコアです。 1 つの Azure リージョンに配置できるハブは 1 つのみです。 Azure portal を使用してハブを作成すると、仮想ハブ VNet と仮想ハブ vpngateway が作成されます。

  ハブ ゲートウェイは、ExpressRoute および VPN Gateway に使用する仮想ネットワーク ゲートウェイと同じではありません。 たとえば、Virtual WAN を使用する場合は、オンプレミス サイトから直接 VNet にサイト間接続を作成しません。 代わりに、ハブへのサイト間接続を作成します。 トラフィックは、常にハブ ゲートウェイを通過します。 これは、VNet には独自の仮想ネットワーク ゲートウェイが必要ないことを意味します。 Virtual WAN により、VNet は、仮想ハブと仮想ハブ ゲートウェイを介して簡単にスケーリングできます。 

* **ハブ仮想ネットワーク接続:** ハブ仮想ネットワーク接続リソースは、ハブを仮想ネットワークにシームレスに接続するために使用します。 現時点では、同じハブ リージョン内にある仮想ネットワークにのみ接続できます。

* **ハブのルート テーブル:** 仮想ハブのルートを作成して、そのルートを仮想ハブのルート テーブルに適用することができます。 仮想ハブのルート テーブルには、複数のルートを適用できます。

## <a name="faq"></a>FAQ

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]


## <a name="next-steps"></a>次の手順

[Virtual WAN パートナーと場所](virtual-wan-locations-partners.md)に関するページを参照します。
