---
title: Azure パブリック IP アドレス プレフィックス
titleSuffix: Azure Virtual Network
description: Azure パブリック IP アドレス プレフィックスとはどのようなもので、パブリック IP アドレスをリソースに割り当てるのにどのように役立つのかについて説明します。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 05/20/2021
ms.author: allensu
ms.openlocfilehash: b2daa54bb17c5eb455be739062c251ed14a0ad13
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707868"
---
# <a name="public-ip-address-prefix"></a>パブリック IP アドレス プレフィックス

パブリック IP アドレス プレフィックスとは、Azure での[パブリック IP アドレス](public-ip-addresses.md#public-ip-addresses)の予約範囲です。 パブリック IP プレフィックスは、各 Azure リージョン内のアドレスのプールから割り当てられます。 Azure リージョンおよびサブスクリプションでパブリック IP アドレス プレフィックスを作成するには、名前とプレフィックス サイズを指定します。 プレフィックス サイズは、使用可能なアドレスの数です。 パブリック IP アドレス プレフィックスは、IPv4 または IPv6 アドレスで構成されます。  Availability Zones のあるリージョンでは、パブリック IP アドレス プレフィックスをゾーン冗長として作成することも、特定の可用性ゾーンに関連付けることもできます。  パブリック IP プレフィックスが作成されたら、パブリック IP アドレスを作成できます。

## <a name="benefits"></a>メリット

- 既知の範囲からの静的パブリック IP アドレス リソースの作成。 プレフィックスを使用して作成したアドレスは、標準 SKU パブリック IP アドレスを割り当てることができる任意の Azure リソースに割り当てることができます。
- 個々のパブリック IP を削除すると、後で再利用できるように予約済みの範囲に *戻されます*。 パブリック IP アドレス プレフィックスの IP アドレスは、プレフィックスを削除するまで使用できるように予約されています。
- プレフィックス範囲内で指定されていて使用可能な IP アドレスを確認することができます。

## <a name="prefix-sizes"></a>プレフィックス サイズ

次のパブリック IP プレフィックスのサイズを使用できます。

-  /28 (IPv4) または /124 (IPv6) = 16 アドレス
-  /29 (IPv4) または /125 (IPv6) = 8 アドレス
-  /30 (IPv4) または /126 (IPv6) = 4 アドレス
-  /31 (IPv4) または /127 (IPv6) = 2 アドレス

プレフィックスのサイズは、クラスレス ドメイン間ルーティング (CIDR) マスク サイズとして指定されます。

サブスクリプションに作成されるプレフィックスの数に制限はありません。 サブスクリプションで使用できる数より多くの静的パブリック IP アドレスが含まれるような範囲の数を作成することはできません。 詳しくは、[Azure での制限](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関するページをご覧ください。

## <a name="scenarios"></a>シナリオ
プレフィックスの静的パブリック IP アドレスには、次のリソースを関連付けることができます。

|リソース|シナリオ|手順|
|---|---|---|
|仮想マシン| プレフィックスのパブリック IP アドレスを、Azure の仮想マシンに関連付けると、ファイアウォールの許可リストに IP アドレスを追加するときの管理オーバーヘッドが軽減されます。 単一のファイアウォール規則でプレフィックス全体を登録できます。 Azure で仮想マシンをスケーリングするときは、同じプレフィックスから IP アドレスを関連付けることで、コスト、時間、管理オーバーヘッドを節約できます。| プレフィックスから仮想マシンに IP アドレスを関連付けるには: </br> 1.[プレフィックスを作成します。](manage-public-ip-address-prefix.md) </br> 2.[プレフィックスから IP アドレスを作成します。](manage-public-ip-address-prefix.md) </br> 3.[仮想マシンのネットワーク インターフェイスに IP アドレスを関連付けます。](./virtual-network-network-interface-addresses.md#add-ip-addresses) </br> [仮想マシン スケール セットに IP を関連付ける](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/)こともできます。
| 標準ロード バランサー | プレフィックスのパブリック IP アドレスを、ロード バランサーのフロントエンド IP 構成またはアウトバウンド規則に関連付けると、Azure のパブリック IP アドレス空間が単純化されます。 連続した IP アドレスの範囲からの送信接続をグルーミングすることで、シナリオを簡略化します。 | プレフィックスからロード バランサーに IP アドレスを関連付けるには: </br> 1.[プレフィックスを作成します。](manage-public-ip-address-prefix.md) </br> 2.[プレフィックスから IP アドレスを作成します。](manage-public-ip-address-prefix.md) </br> 3.ロード バランサーを作成するときに、ロード バランサーのフロントエンド IP アドレスとして、上の手順 2 で作成した IP アドレスを選択または更新します。 |
| Azure Firewall | 送信 SNAT に対してプレフィックスのパブリック IP アドレスを使用できます。 すべての送信仮想ネットワーク トラフィックが [Azure Firewall](../../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) のパブリック IP アドレスに変換されます。 | プレフィックスの IP をファイアウォールに関連付けるには: </br> 1.[プレフィックスを作成します。](manage-public-ip-address-prefix.md) </br> 2.[プレフィックスから IP アドレスを作成します。](manage-public-ip-address-prefix.md) </br> 3.[Azure Firewall をデプロイする](../../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)ときに、前にプレフィックスから指定した IP アドレスを選択します。|
| VPN Gateway (AZ SKU)、Application Gateway v2、NAT Gateway | ゲートウェイに対してプレフィックスのパブリック IP アドレスを使用できます | プレフィックスの IP をゲートウェイに関連付けるには: </br> 1.[プレフィックスを作成します。](manage-public-ip-address-prefix.md) </br> 2.[プレフィックスから IP アドレスを作成します。](manage-public-ip-address-prefix.md) </br> 3. [VPN Gateway](../../vpn-gateway/tutorial-create-gateway-portal.md)、[Application Gateway](../../application-gateway/quick-create-portal.md#create-an-application-gateway)、または [NAT Gateway](../nat-gateway/tutorial-create-nat-gateway-portal.md) をデプロイするときに、前にプレフィックスから指定した IP アドレスを選択します。|

また、パブリック IP アドレス プレフィックス リソースは、特定のリソースによって直接利用できます。

リソース|シナリオ|手順|
|---|---|---|
|仮想マシン スケール セット | パブリック IP アドレス プレフィックスを使用して、仮想マシン スケール セット内でインスタンス レベルの IP を生成できます。ただし、個々のパブリック IP リソースは作成されません。 | スケール セットの作成の一環として、パブリック IP 構成にこのプレフィックスを使用する手順が記載された[テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vmss-with-public-ip-prefix)を使用します。 (プレフィックスのゾーン プロパティは、出力には表示されませんがインスタンスの IP に渡されます。詳細については、[仮想マシン スケール セットのネットワーク](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine)に関するページを参照してください。) |
| 標準ロード バランサー | パブリック IP アドレス プレフィックスを使用して、[送信接続の範囲内のすべての IP を使用](../../load-balancer/outbound-rules.md#scale)してロード バランサーをスケーリングできます。 | プレフィックスをロード バランサーに関連付けるには: </br> 1.[プレフィックスを作成します。](manage-public-ip-address-prefix.md) </br> 2. ロード バランサーを作成するときに、IP プレフィックスを選択してロード バランサーのフロントエンドとして関連付けます。 |
| NAT Gateway | パブリック IP プレフィックスを使用して、送信接続のプレフィックスに含まれるパブリック IP を使用して NAT Gateway をスケーリングできます。 | プレフィックスを NAT Gateway に関連付けるには: </br> 1.[プレフィックスを作成します。](manage-public-ip-address-prefix.md) </br> 2. NAT Gateway を作成するときに、送信 IP として IP プレフィックスを選択します。 |
| VPN Gateway (AZ SKU) または Application Gateway v2 | ゾーン冗長 VPN または Application Gateway v2 に対してプレフィックスのパブリック IP を使用できます。 | プレフィックスの IP をゲートウェイに関連付けるには: </br> 1.[プレフィックスを作成します。](manage-public-ip-address-prefix.md) </br> 2.[プレフィックスから IP アドレスを作成します。](manage-public-ip-address-prefix.md) </br> 3. [VPN Gateway](../../vpn-gateway/tutorial-create-gateway-portal.md) または [Application Gateway](../../application-gateway/quick-create-portal.md#create-an-application-gateway) をデプロイするときに、前にプレフィックスから指定した IP アドレスを選択します。|

## <a name="limitations"></a>制限事項

- プレフィックスに対して IP アドレスを指定することはできません。 プレフィックスの IP アドレスは、ユーザーが指定したサイズに基づいて、Azure によって指定されます。  さらに、プレフィックスから作成されるすべてのパブリック IP アドレスは、プレフィックスと同じ Azure リージョンおよびサブスクリプションに存在する必要があります。 アドレスは、同じリージョンおよびサブスクリプション内のリソースに割り当てる必要があります。
- 最大 16 個の IP アドレスのプレフィックスを作成できます。 詳細については、[ネットワークの上限の引き上げ要求](../../azure-portal/supportability/networking-quota-requests.md)に関するページおよび [Azure の制限](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関するページを参照してください。
- プレフィックスが作成された後に、範囲のサイズを変更することはできません。
- プレフィックスの範囲から割り当てることができるのは、Standard SKU で作成された静的パブリック IP アドレスのみです。 パブリック IP アドレスの SKU の詳細については、「[パブリック IP アドレス](public-ip-addresses.md#public-ip-addresses)」をご覧ください。
- 範囲のアドレスを割り当てることができるのは、Azure Resource Manager リソースのみです。 クラシック デプロイ モデルのリソースにアドレスを割り当てることはできません。
- 範囲内のいずれかのアドレスが、リソースに関連付けられているパブリック IP アドレス リソースに割り当てられている場合は、プレフィックスを削除できません。 最初に、プレフィックスの IP アドレスを割り当てられているすべてのパブリック IP アドレス リソースの関連付けを解除する必要があります。 パブリック IP アドレスの関連付け解除の詳細については、「[パブリック IP アドレスの管理](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)」を参照してください。
- パブリック IP アドレス プレフィックスは、現在、*インターネット* **ルーティング設定** または *グローバル* **レベル** と互換性がありません (リージョン間の負荷分散の場合)。
- IPv6 は、基本パブリック IP では **動的** 割り当ての使用のみがサポートされています。 動的割り当てとは、Azure でリソースを削除して再デプロイすると、IPv6 アドレスが変更されることを意味します。 
- Standard IPv6 パブリック IP では、静的 (予約済み) 割り当てがサポートされます。 
- Standard 内部ロード バランサーは、割り当てられているサブネット内からの動的割り当てをサポートします。  

## <a name="pricing"></a>価格
 
Azure パブリック IP の使用 (個々の IP アドレスと IP の範囲の両方) に関連するコストについては、「[パブリック IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses/)」をご覧ください。

## <a name="next-steps"></a>次のステップ

- パブリック IP アドレス プレフィックスを[作成](manage-public-ip-address-prefix.md)する