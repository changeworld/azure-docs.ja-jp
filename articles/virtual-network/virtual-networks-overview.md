---
title: Azure Virtual Network | Microsoft Docs
description: "Azure Virtual Network の概念と機能について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2017
ms.author: jdial
ms.openlocfilehash: 55aece3f20ee98d21d7bb2b96cb3d039d4849f8f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="azure-virtual-network"></a>Azure Virtual Network

Microsoft Azure Virtual Network サービスでは、Azure リソースを利用して、仮想ネットワーク内の他のサービスと安全に相互通信できるようにしています。 仮想ネットワークは、サブスクリプション専用の Azure クラウドを論理的に分離したものです。 他の仮想ネットワークまたはオンプレミス ネットワークに仮想ネットワークを接続できます。 次の図は、Azure Virtual Network サービスの機能の一部を示しています。

![ネットワーク図](./media/virtual-networks-overview/virtual-network-overview.png)

次の Azure Virtual Network の機能の詳細については、各機能をクリックしてください。
- **[分離:](#isolation)** 仮想ネットワークは相互に分離されます。 同じ CIDR (10.0.0.0/0 など) アドレス ブロックを使用する開発、テスト、運用環境に個別の仮想ネットワークを作成できます。 逆に、異なる CIDR アドレス ブロックを使用する複数の仮想ネットワークを作成し、ネットワークをまとめて接続することもできます。 仮想ネットワークは、複数のサブネットに分割できます。 Azure では、仮想ネットワークにデプロイされたリソースの内部名前解決が提供されます。 必要に応じて、Azure の内部名前解決を使用する代わりに、独自の DNS サーバーを使用するよう仮想ネットワークを構成できます。
- **[インターネット通信:](#internet)** 仮想ネットワークにデプロイされている仮想マシンなどのリソースは、既定でインターネットにアクセスできます。 また、必要に応じて、特定のリソースへの着信アクセスを有効にすることもできます。
- **[Azure リソース通信:](#within-vnet)** 仮想ネットワークにデプロイされている Azure リソースは、リソースが異なるサブネットにデプロイされている場合でも、プライベート IP アドレスを使用して相互に通信できます。 Azure では、サブネット、接続された仮想ネットワーク、オンプレミスのネットワークの間に既定のルーティングを提供しているため、ルートの構成と管理は必要ありません。 必要な場合は、Azure のルーティングをカスタマイズできます。
- **[仮想ネットワークの接続:](#connect-vnets)** 仮想ネットワークは、任意の仮想ネットワーク内のリソースが他の仮想ネットワーク内のリソースと通信しすることで、相互に接続できます。
- **[オンプレミスの接続性:](#connect-on-premises)** 仮想ネットワークをオンプレミスのネットワークに接続して、リソースが相互に通信できるようにすることができます。
- **[トラフィックのフィルター処理:](#filtering)** 仮想ネットワーク内のリソースとの間のネットワーク トラフィックを、送信元 IP アドレスおよびポート、送信先 IP アドレスおよびポート、プロトコルでフィルター処理できます。
- **[ルーティング:](#routing)** 必要に応じて、独自のルートを構成するかネットワーク ゲートウェイ経由で BGP ルートをプロパゲートすることで、Azure の既定のルーティングを上書きできます。

## <a name = "isolation"></a>ネットワークの分離とセグメント化

各 Azure [サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)と Azure [リージョン](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region)内に複数の仮想ネットワークを実装できます。 各仮想ネットワークは、他の仮想ネットワークから分離されています。 各仮想ネットワークに対して、次の操作を実行できます。
- パブリックおよびプライベート (RFC 1918) アドレスを使用して、カスタム プライベート IP アドレス空間を指定する。 Azure は、ユーザー自身が割り当てるアドレス空間のプライベート IP アドレスに、仮想ネットワーク内のリソースを割り当てる。
- 仮想ネットワークを 1 つ以上のサブネットに分割し、各サブネットに仮想ネットワークのアドレス空間の一部を割り当てる。
- Azure で提供される名前解決を使用するか、仮想ネットワーク内のリソースで使用する独自の DNS サーバーを指定する。 仮想ネットワークでの名前解決について詳しくは、[仮想ネットワーク内のリソースの名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md)に関する記事をご覧ください。

## <a name = "internet"></a>インターネット通信
仮想ネットワーク内のすべてのリソースでは、インターネットへ送信するための通信が可能です。 既定では、リソースのプライベート IP アドレスは、Azure インフラストラクチャによって選択されたパブリック IP アドレスへの送信元ネットワーク アドレス変換 (SNAT) が行われています。 インターネットへの送信接続の詳細については、「[Azure の送信用接続の詳細](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address)」を参照してください。 インターネットへの送信接続を回避するために、カスタム ルートまたはトラフィック フィルターを実装できます。

インターネットから Azure リソースへの着信接続、または SNAT なしでインターネットへの送信接続を行うには、リソースにパブリック IP アドレスを割り当てる必要があります。 パブリック IP アドレスの詳細については、「[パブリック IP アドレス](virtual-network-public-ip-address.md)」を参照してください。

## <a name="within-vnet"></a>Azure リソース間の安全な通信

仮想ネットワーク内に仮想マシンをデプロイできます。 仮想マシンは、ネットワーク インターフェイス経由で仮想ネットワークの他のリソースと通信します。 ネットワーク インターフェイスの詳細については、「[ネットワーク インターフェイス](virtual-network-network-interface.md)」をご覧ください。

Azure App Service Environment や Azure Virtual Machine Scale Sets などの仮想ネットワークに、他のいくつかの種類の Azure リソースをデプロイすることもできます。 仮想ネットワークにデプロイできる Azure リソースの詳細な一覧については、[Azure サービスの仮想ネットワーク サービスの統合](virtual-network-for-azure-services.md)に関するページをご覧ください。

一部のリソースは仮想ネットワークにデプロイできませんが、リソースへの通信を仮想ネットワーク内だけに制限することはできます。 リソースへのアクセスを制限する方法について詳しくは、「[仮想ネットワークのサービス エンドポイント](virtual-network-service-endpoints-overview.md)」をご覧ください。 

## <a name="connect-vnets"></a>仮想ネットワークを接続する

いずれかの仮想ネットワーク内のリソースが仮想ネットワークのピアリングを使用して相互に通信することで、仮想ネットワークを相互に接続できます。 異なる仮想ネットワークにあるリソース間の通信の帯域幅と待機時間は、リソースが同じ仮想ネットワーク内にある場合と同じです。 ピアリングの詳細については、「[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)」を参照してください。

## <a name="connect-on-premises"></a>オンプレミス ネットワークに接続する

オンプレミス ネットワークを仮想ネットワークに接続するには、次のオプションを組み合わせて使用します。
- **ポイント対サイト仮想プライベート ネットワーク (VPN):** ネットワーク内の仮想ネットワークと 1 台の PC の間で確立されます。 仮想ネットワークとの接続を確立する各 PC では、個別に接続を構成する必要があります。 この接続の種類は、既存のネットワークへの変更をほとんどまたはまったく必要としないため、Azure を使い始めたばかりのユーザーまたは開発者に適しています。 接続は、SSTP プロトコルを使用して、PC と仮想ネットワーク間にインターネット経由の暗号化された通信を提供します。 トラフィックがインターネットを経由するため、ポイント対サイト VPN の待ち時間は予測できません。
- **サイト間 VPN:** 仮想ネットワークにデプロイされた VPN デバイスと Azure VPN Gateway の間で確立されます。 この接続の種類を使用すると、承認した任意のオンプレミス リソースが仮想ネットワークにアクセスできます。 この接続は IPSec/IKE VPN で、オンプレミスのデバイスと Azure VPN ゲートウェイの間にインターケット経由の暗号化された通信を提供します。 トラフィックがインターネットを経由するため、サイト間接続の待ち時間は予測できません。
- **Azure ExpressRoute:** ExpressRoute のパートナーを介して、ネットワークと Azure の間で確立されます。 この接続はプライベート接続です。 トラフィックはインターネットを経由しません。 トラフィックがインターネットを経由しないため、ExpressRoute 接続の待ち時間は予測可能です。

ここまでに説明したすべての接続オプションについて詳しくは、「[接続トポロジの図](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams)」をご覧ください。

## <a name="filtering"></a>ネットワーク トラフィックをフィルター処理する
次のオプションのいずれかまたは両方を使用して、サブネット間のネットワーク トラフィックをフィルター処理できます。
- **ネットワーク セキュリティ グループ:** 各ネットワーク セキュリティ グループには、送信元と送信先の IP アドレス、ポート、およびプロトコルでトラフィックをフィルター処理できるようにする受信と送信のセキュリティ規則を複数含めることができます。 ネットワーク セキュリティ グループは、仮想マシン内の各ネットワーク インターフェイスに適用できます。 また、ネットワーク セキュリティ グループは、ネットワーク インターフェイスや他の Azure リソースが含まれているサブネットにも適用できます。 ネットワーク セキュリティ グループの詳細については、[ネットワーク セキュリティ グループ](security-overview.md#network-security-groups)に関するページをご覧ください。
- **ネットワーク仮想アプライアンス:** ネットワーク仮想アプライアンスとは、ファイアウォールなどのネットワーク機能を実行するソフトウェアが動作している仮想マシンです。 利用可能なネットワーク仮想アプライアンスの一覧については、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) で確認してください。 WAN の最適化やその他のネットワーク トラフィック機能を提供するネットワーク仮想アプライアンスも入手できます。 通常、ネットワーク仮想アプライアンスは、ユーザー定義ルートまたは BGP ルートで使用されます。 また、ネットワーク仮想アプライアンスを使用して、仮想ネットワーク間のトラフィックをフィルター処理することもできます。

## <a name="routing"></a>ネットワーク トラフィックをルーティングする

Azure では、仮想ネットワーク内の任意のサブネットに接続されている複数のリソースの相互通信とインターネット通信を可能にするルート テーブルが既定で作成されます。 次のオプションのいずれかまたは両方を実装して、Azure によって作成される既定のルートを上書きできます。
- **ユーザー定義ルート:** サブネットごとにトラフィックのルーティング先を制御するルートを含むカスタム ルート テーブルを作成できます。 ユーザー定義ルートの詳細については、[ユーザー定義ルート](virtual-networks-udr-overview.md#user-defined)に関するページをご覧ください。
- **BGP のルート:** Azure VPN ゲートウェイまたは ExpressRoute 接続を使用して仮想ネットワークをオンプレミス ネットワークに接続する場合、BGP ルートを仮想ネットワークに伝達できます。

## <a name="pricing"></a>価格

仮想ネットワーク、サブネット、ルート テーブル、ネットワーク セキュリティ グループには料金がかかりません。 送信インターネット帯域幅の使用量、パブリック IP アドレス、仮想ネットワーク ピアリング、VPN ゲートウェイ、ExpressRoute それぞれには、独自の料金体系が用意されています。 詳細については、[Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network)、[VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway)、[ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) の価格に関するページを参照してください。

## <a name="faq"></a>FAQ

Azure Virtual Network についてよく寄せられる質問を確認するには、[Virtual Network の FAQ](virtual-networks-faq.md) に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

- 「[最初の仮想ネットワークの作成](quick-create-portal.md)」の手順を実行して、最初の仮想ネットワークを作成し、そこにいくつかの仮想ネットワークをデプロイします。
- [ポイント対サイト接続の構成](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページの手順を実行して、仮想ネットワークへのポイント対サイト接続を作成します。
- Azure のその他の重要な[ネットワーク機能](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)について参照してください。
