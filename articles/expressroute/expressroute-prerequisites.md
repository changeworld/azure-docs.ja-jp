---
title: "Azure ExpressRoute 導入のための前提条件 |Microsoft ドキュメント"
description: "このページは、Azure の ExpressRoute 回線を注文する前に満たすための要件の一覧を示します。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: f872d25e-acfd-405d-9d1b-dcb9f323a2ff
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/30/2017
ms.author: cherylmc
ms.openlocfilehash: 8629235511e0dda149ceef6a9c834c3042f64f90
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute 前提条件 & チェックリスト
ExpressRoute を使用して Microsoft クラウド サービスに接続するには、次のセクションで、次の要件を満たしていることを確認する必要があります。

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure アカウント
* 有効でアクティブな Microsoft Azure アカウント。 ExpressRoute 回線を設定するには、このアカウントが必要です。 ExpressRoute 回線は、Azure サブスクリプション内のリソースです。 Azure サブスクリプションは、場合でも、接続など、Office 365 サービスと Dynamics 365、Azure 以外の Microsoft クラウド サービスに制限されますが、要件です。
* アクティブな Office 365 サブスクリプション (Office 365 サービスの場合)。 詳細については、次を参照してください。、[固有の要件を Office 365](#office-365-specific-requirements)この記事のセクションです。

## <a name="connectivity-provider"></a>接続プロバイダー

* 使用する、 [ExpressRoute 接続パートナー](expressroute-locations.md#partners) Microsoft のクラウドに接続します。 内部設置型ネットワークと Microsoft 間の接続を設定することができます[3 つの方法](expressroute-introduction.md)です。
* 介した Microsoft のクラウドに接続できる、プロバイダーは ExpressRoute 接続パートナーではない場合、[クラウド交換プロバイダー](expressroute-locations.md#connectivity-through-exchange-providers)です。

## <a name="network-requirements"></a>ネットワークの要件
* **接続の冗長化**: 物理的な接続して、プロバイダー間の冗長性要件はありません。 Microsoft をセットアップする Microsoft のルーターとルーターの間、ピアリング、だけがある場合にも冗長の BGP セッションを必要[クラウド exchange への 1 つの物理接続](expressroute-faqs.md#onep2plink)です。
* **ルーティング**: によって、Microsoft のクラウドに接続する方法、またはプロバイダー必要を設定して、BGP セッションを管理[ルーティング ドメイン](expressroute-circuit-peerings.md)です。 一部のイーサネット接続プロバイダーまたは交換プロバイダーのクラウドと BGP の管理を提供できます、付加価値サービス。
* **NAT**: マイクロソフトでは、Microsoft ピアリング経由のパブリックの IP アドレスのみ使用できます。 か、プロバイダーが、パブリック IP アドレスをプライベート IP アドレスを変換する必要があります、内部設置型ネットワーク内のプライベート IP アドレスを使用している場合[、NAT を使用して](expressroute-nat.md)です。
* **QoS**: Skype を必要とするさまざまなサービス (音声、ビデオ、テキストなど) には、ビジネス区別 QoS 処理します。 プロバイダーが従う必要があります、 [QoS 要件](expressroute-qos.md)です。
* **ネットワーク セキュリティ**: 検討[ネットワーク セキュリティ](../best-practices-network-security.md)ExpressRoute 経由で Microsoft のクラウドに接続するときにします。

## <a name="office-365"></a>Office 365
ExpressRoute での Office 365 を有効にする場合は、Office 365 の要件に関する詳細については、次のドキュメントを確認します。

* [Office 365 の ExpressRoute の概要](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Office 365 の ExpressRoute のルーティング](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Office 365 Url および IP アドレス範囲](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [ネットワークの計画と Office 365 のパフォーマンスの調整](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [ネットワーク帯域幅の電卓とツール](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [内部設置型環境と office 365 の統合](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Office 365 での ExpressRoute の詳細のトレーニング ビデオ](https://channel9.msdn.com/series/aer/)

## <a name="dynamics-365"></a>Dynamics 365
Dynamics 365 ExpressRoute で有効にすることを計画する場合は、Dynamics 365 の詳細については、次のドキュメントを確認します。

* [Dynamics 365 と ExpressRoute のホワイト ペーパー](http://download.microsoft.com/download/B/2/8/B2896B38-9832-417B-9836-9EF240C0A212/Microsoft%20Dynamics%20365%20and%20ExpressRoute.pdf)
* [Dynamics 365 Url](https://support.microsoft.com/kb/2655102)と[IP アドレスの範囲](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>次のステップ
* ExpressRoute の詳細については、次を参照してください。、 [ExpressRoute のよく寄せられる質問](expressroute-faqs.md)です。
* ExpressRoute 接続プロバイダーを検索します。 参照してください[ExpressRoute パートナーとピアリング場所](expressroute-locations.md)です。
* 要件を参照してください[ルーティング](expressroute-routing.md)、 [NAT](expressroute-nat.md)、および[QoS](expressroute-qos.md)です。
* ExpressRoute 接続を構成します。
  * [ExpressRoute 回線を作成します。](expressroute-howto-circuit-classic.md)
  * [ルーティングを構成します。](expressroute-howto-routing-classic.md)
  * [VNet を ExpressRoute 回線にリンクします。](expressroute-howto-linkvnet-classic.md)
