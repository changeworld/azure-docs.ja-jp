---
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/02/2021
ms.author: cherylmc
ms.openlocfilehash: cf11d4614cd4b6c3c387ef83b63da506a1702afc
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2021
ms.locfileid: "111411925"
---
### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub-can-i-join-this-partner-program"></a>ネットワーク アプライアンス パートナーとして、提供する NVA をハブに追加したいと思っています。 このパートナー プログラムに参加することはできますか?

残念ながら、現時点では、新しいパートナー オファーをオンボードするキャパシティがございません。 後日、再度お問い合わせください。

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Azure Marketplace から Virtual WAN ハブに任意の NVA をデプロイすることはできますか?

現時点で Virtual WAN ハブにデプロイできるのは、[Barracuda CloudGen WAN](https://aka.ms/BarracudaMarketPlaceOffer)、[Cisco Cloud vWAN Application](https://azuremarketplace.microsoft.com/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview)、[VMware Sd-WAN](https://aka.ms/vmwareMarketplaceLink) のみになります。

### <a name="what-is-the-cost-of-the-nva"></a>NVA のコストはいくらですか?

NVA ベンダーから NVA のライセンスを購入する必要があります。 Barracuda ライセンスの Barracuda CloudGen WAN NVA については、[Barracuda の CloudGen WAN のページ](https://www.barracuda.com/products/cloudgenwan)を参照してください。 現在 Cisco では、CISCO から直性購入する必要がある BYOL (ライセンス持ち込み) のライセンス モデルのみが提供されています。 また、Microsoft からは消費した NVA インフラストラクチャ ユニットと、使用したその他のリソースについても料金が発生します。 詳細については、[価格の概念](../articles/virtual-wan/pricing-concepts.md)に関するページを参照してください。

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>NVA を Basic ハブにデプロイすることはできますか?

いいえ。 NVA をデプロイする場合は、Standard ハブを使用する必要があります。

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>NVA をセキュリティで保護されたハブにデプロイすることはできますか?

はい。 パートナーの NVA は、Azure Firewall を使用するハブにデプロイすることができます。

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>ブランチ オフィス内のすべての Barracuda デバイスをハブの CloudGen WAN NVA に接続することはできますか?

いいえ。 Barracuda CloudGen WAN は、Barracuda CPE デバイスのみと互換性があります。 CloudGen WAN の要件の詳細については、[Barracuda の CloudGen WAN のページ](https://www.barracuda.com/products/cloudgenwan)を参照してください。 Cisco の場合、互換性がある SD-WAN CPE デバイスがいくつかあります。 互換性がある CPE については、[Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) のドキュメントを参照してください。

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>ハブの NVA ではどのようなルーティング シナリオがサポートされていますか?

ハブの NVA では、Virtual WAN でサポートされているすべてのルーティング シナリオがサポートされています。
