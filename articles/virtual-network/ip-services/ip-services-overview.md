---
title: Azure Virtual Network IP サービスとは
description: Azure Virtual Network IP サービスの概要 IP サービスの仕組みと、Azure で IP リソースを使用する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subService: ip-services
ms.topic: overview
ms.date: 10/01/2021
ms.custom: template-overview
ms.openlocfilehash: 7b9c1eb8e1e0a067da2f0ed29762ef14ce365690
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257692"
---
# <a name="what-is-azure-virtual-network-ip-services"></a>Azure Virtual Network IP サービスとは

IP サービスとは、Azure Virtual Network での通信を可能にする一連の IP アドレス関連サービスです。 Azure では、リソース間の通信にパブリックおよびプライベート IP アドレスが使用されます。 リソースとの通信は、プライベートの Azure Virtual Network とパブリック インターネットで行われます。

IP サービスは次の要素で構成されます。

* パブリック IP アドレス

* パブリック IP アドレス プレフィックス

* プライベート IP アドレス

* ルーティング設定

* 従量制課金なしのルーティング優先設定

## <a name="public-ip-addresses"></a>パブリック IP アドレス

パブリック IP は、インターネット リソースによって Azure 内のリソースへのインバウンド通信を行うために使用されます。 パブリック IP アドレスは、IPv4 または IPv6 アドレスを使用して作成できます。 IPv4 アドレスと IPv6 アドレスを使用してデュアルスタック デプロイメントを作成するオプションが表示される場合があります。 パブリック IP アドレスは **Standard** と **Basic** の SKU で使用できます。 パブリック IP アドレスは、静的または動的に割り当てることができます。

パブリック IP アドレスは、独自のプロパティを備えたリソースです。 パブリック IP アドレスを関連付けることができるリソースには次のようなものがあります。

* 仮想マシン ネットワーク インターフェイス
* インターネットに接続するロード バランサー
* Virtual Network ゲートウェイ (VPN/ER)
* NAT Gateway
* アプリケーション ゲートウェイ
* Azure Firewall
* bastion ホスト

パブリック IP アドレスの詳細については、「[パブリック IP アドレス](./public-ip-addresses.md)」および「[Azure パブリック IP アドレスを作成、変更、または削除する](./virtual-network-public-ip-address.md)」を参照してください。

## <a name="public-ip-address-prefixes"></a>パブリック IP アドレス プレフィックス

パブリック IP プレフィックスは、Azure の IP アドレスの予約済みの範囲です。 パブリック IP アドレス プレフィックスは、IPv4 または IPv6 アドレスで構成されます。  Availability Zones のあるリージョンでは、パブリック IP アドレス プレフィックスをゾーン冗長として作成することも、特定の可用性ゾーンに関連付けることもできます。 パブリック IP プレフィックスが作成されたら、パブリック IP アドレスを作成できます。

次のパブリック IP プレフィックスのサイズを使用できます。

-  /28 (IPv4) または /124 (IPv6) = 16 アドレス
-  /29 (IPv4) または /125 (IPv6) = 8 アドレス
-  /30 (IPv4) または /126 (IPv6) = 4 アドレス
-  /31 (IPv4) または /127 (IPv6) = 2 アドレス

プレフィックスのサイズは、クラスレス ドメイン間ルーティング (CIDR) マスク サイズとして指定されます。

サブスクリプションに作成されるプレフィックスの数に制限はありません。 サブスクリプションで使用できる数より多くの静的パブリック IP アドレスが含まれるような範囲の数を作成することはできません。 詳しくは、[Azure での制限](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関するページをご覧ください。

パブリック IP アドレス プレフィックスの詳細については、「[パブリック IP アドレス プレフィックス](./public-ip-address-prefix.md)」と「[パブリック IP アドレス プレフィックスの作成、変更、削除](./manage-public-ip-address-prefix.md)」を参照してください。

## <a name="private-ip-addresses"></a>プライベート IP アドレス

プライベート IP を使用すると、Azure 内のリソース間の通信が可能になります。 Azure では、プライベート IP アドレスは、リソースの存在する仮想ネットワーク サブネットのアドレス範囲からリソースに割り当てられます。 Azure のプライベート IP アドレスは、静的または動的に割り当てられます。

プライベート IP アドレスを関連付けることができるリソースには次のようなものがあります。

* 仮想マシン
* 内部ロード バランサー
* アプリケーション ゲートウェイ
* プライベート エンドポイント

プライベート IP アドレスの詳細については、「[プライベート IP アドレス](./private-ip-addresses.md)」を参照してください。

## <a name="routing-preference"></a>ルーティング設定

Azure のルーティング優先設定を使用すると、Azure とインターネットにトラフィックをルーティングする方法を選択できます。 Microsoft ネットワーク経由または ISP ネットワーク (パブリック インターネット) 経由のどちらでトラフィックをルーティングするかを選択できます。 パブリック IP アドレスを作成するときに、ルーティング オプションを選択できます。 既定では、すべての Azure サービスについて、トラフィックは Microsoft グローバル ネットワーク経由でルーティングされます。 

ルーティング設定の選択肢は次のとおりです。

* **Microsoft ネットワーク** - イングレス トラフィックもエグレス トラフィックも、移動のほとんどは Microsoft グローバル ネットワーク上で行われます。 このルーティングは "*コールド ポテト*" ルーティングとも呼ばれます。

* **パブリック インターネット (ISP ネットワーク)** - 新しいルーティングの選択であるインターネット ルーティングでは、Microsoft グローバル ネットワーク上の移動が最小限に抑えられ、トランジット ISP ネットワークを使用してトラフィックがルーティングされます。 このルーティングは "*ホット ポテト*" ルーティングとも呼ばれます。

ルーティング設定の詳細については、「[ルーティング設定とは](./routing-preference-overview.md)」を参照してください。

## <a name="routing-preference-unmetered"></a>従量制課金なしのルーティング優先設定

従量制課金なしのルーティング優先設定は、顧客が Azure で元のコンテンツをホストしているコンテンツ配信ネットワーク (CDN) プロバイダーで使用できます。 このサービスを使用すると、CDN プロバイダーは、さまざまな場所で Microsoft グローバル ネットワーク エッジ ルーターとの直接ピアリング接続を確立できます。

Azure の配信元から CDN プロバイダーに送信されるネットワーク トラフィックには、直接接続の利点があります。

* これらの直接リンクを介してルーティングされる Azure リソースからのトラフィック送信のデータ転送課金は無料です。

* Azure の CDN プロバイダーと配信元の間の直接接続では、間にホップが存在しないため、最適なパフォーマンスが得られます。 この接続は、配信元からデータを頻繁にフェッチする CDN ワークロードに役立ちます。

従量制課金なしのルーティング優先設定の詳細については、「[従量制課金なしのルーティング優先設定とは](./routing-preference-unmetered.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

IP サービス リソースの作成を開始します。

- [Azure portal を使用してパブリック IP アドレスを作成する](./create-public-ip-portal.md)。
- [Azure portal を使用してパブリック IP アドレス プレフィックスを作成する](./create-public-ip-prefix-portal.md)。
- [Azure portal を使用して VM のプライベート IP アドレスを構成する](./virtual-networks-static-private-ip-arm-pportal.md)。
- [Azure portal を使用してパブリック IP アドレスのルーティング優先設定を構成する](./routing-preference-portal.md)。