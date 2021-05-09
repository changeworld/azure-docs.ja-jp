---
title: Private Zones のシナリオ - Azure DNS
description: この記事では、Azure DNS Private Zones を使用するための一般的なシナリオについて説明します。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/27/2021
ms.author: rohink
ms.openlocfilehash: 5a2572af0fd312efeacb8544b653db1b35809244
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108127823"
---
# <a name="azure-dns-private-zones-scenarios"></a>Azure DNS Private Zones のシナリオ

Azure DNS Private Zones を使用すると、仮想ネットワーク内および仮想ネットワーク間での名前解決を実現できます。 この記事では、この機能を使用することでメリットを得られるいくつかの一般的なシナリオについて見ていきます。

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>シナリオ: 単一の仮想ネットワークをスコープとする名前解決

このシナリオでは、仮想マシンなどの多くのリソースを含む Azure 内に仮想ネットワークを確保しているとします。 特定のドメイン名 (DNS ゾーン) を使用して、仮想ネットワーク内のどのリソースも解決することが、求められる要件となります。 また、名前解決はプライベートとし、インターネットからアクセスできないようにする必要があります。 最後に、Azure で VM を DNS ゾーンに自動的に登録する必要があります。

このシナリオを次に示します。 "A" という名前の仮想ネットワークがあり、これには 2 つの VM (VNETA-VM1 と VNETA-VM2) が含まれています。 各 VM には、プライベート IP が関連付けられています。 プライベート ゾーン (たとえば、`contoso.com`) を作成したら、仮想ネットワーク "A" を登録仮想ネットワークとしてリンクします。 Azure DNS によって、この 2 つの VM を参照する 2 つの A レコードがゾーン内に自動的に作成されます。 VNETA-VM1 からの DNS クエリを実行することで `VNETA-VM2.contoso.com` を解決できるようになり、VNETA-VM2 のプライベート IP を含む DNS 応答が返されます。
また、VNETA-VM1 (10.0.0.1) のプライベート IP のために、VNETA-VM2 から逆引き DNS クエリ (PTR) を行うこともできます。 DNS 応答には、予想どおりに VNETA-VM1 という名前が含められます。 

![単一仮想ネットワークの解決](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>シナリオ: 仮想ネットワーク間での名前解決

このシナリオでは、プライベート ゾーンを複数の仮想ネットワークに関連付ける必要があります。 このソリューションは、ハブアンドスポーク モデルなどのさまざまなネットワーク アーキテクチャに実装することができます。 この構成は、中央ハブ仮想ネットワークを使用して複数のスポーク仮想ネットワークを接続するときに使用されます。 中央ハブ仮想ネットワークを登録仮想ネットワークとしてリンクでき、スポーク仮想ネットワークを解決仮想ネットワークとしてリンクできます。 

次の図は、このシナリオを簡略化したバージョンを示していて、含まれる仮想ネットワークは A と B の 2 つだけです。A が登録仮想ネットワークとして定義され、B が解決仮想ネットワークとして定義されます。 この目的は、両方の仮想ネットワークで、共通のゾーン `contoso.com` が共有されることにあります。 ゾーンが作成されると、登録用として定義されている仮想ネットワークによって、仮想ネットワーク内の VM (VNETA-VM1 と VNETA-VM2) の DNS レコードが自動的に登録されます。 解決仮想ネットワーク B 内の VM 用のゾーンに DNS レコードを手動で追加することもできます。この設定を使用すると、正引きおよび逆引き DNS クエリにおける次の動作が観察されます。
* 解決仮想ネットワーク B 内の VNETB VM1 から VNETA-VM1.contoso.com に対する DNS クエリは、VNETA-VM1 のプライベート IP を含む DNS 応答を受信します。
* 解決仮想ネットワーク B 内の VNETB-VM2 からの 10.1.0.1 に対する逆引き DNS (PTR) クエリは、FQDN VNETB-VM1.contoso.com を含む DNS 応答を受信します。  
* 解決仮想ネットワーク B 内の VNETB-VM3 からの 10.0.0.1 に対する逆引き DNS (PTR) クエリは、NXDOMAIN を受信します。 その理由は、逆引き DNS クエリのスコープが、同じ仮想ネットワークに限定されているためです。 

![複数の仮想ネットワークの解決](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>シナリオ: 水平分割機能

このシナリオでは、同じ DNS ゾーンでクライアントが配置されている場所に応じて異なる名前解決が必要になります。 機能または動作が異なるアプリケーションのプライベートおよびパブリック バージョンを持っている場合があります。 両方のバージョンに対して同じドメイン名を使用する必要がありました。 このシナリオを実現するには、Azure DNS 内に同じ名前を使用してパブリックおよびプライベート ゾーンを作成します。 

次の図にこのシナリオを示します。 仮想ネットワーク A には、2 つの VM (VNETA-VM1 と VNETA-VM2) が含まれています。 どちらにもプライベート IP とパブリック IP が構成されています。 `contoso.com` という名前のパブリック DNS ゾーンが作成されました。これによって、これらの VM のパブリック IP が DNS レコードとしてゾーン内に登録されます。 `contoso.com` という名前のプライベート DNS ゾーンも作成されます。 仮想ネットワーク A は、登録仮想ネットワークとして定義してあります。 次に、Azure によって、VM がそれぞれのプライベート IP を指す A のレコードとしてプライベート ゾーンに自動的に登録されます。

これで、インターネット クライアントが `VNETA-VM1.contoso.com` に対して DNS クエリを実行すると、Azure によって、パブリック ゾーンからパブリック IP レコードが返されます。 同じ DNS クエリが、同じ仮想ネットワーク A 内の別の VM (たとえば VNETA-VM2) から発行された場合、Azure は、プライベート ゾーンからプライベート IP レコードを返します。 

![分割 Brian 解決](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>次のステップ
プライベート DNS ゾーンの詳細については、「[Using Azure DNS for private domains (プライベート ドメインでの Azure DNS の使用)](private-dns-overview.md)」をご覧ください。

Azure DNS で[プライベート DNS ゾーンを作成する](./private-dns-getstarted-powershell.md)方法を学びます。

「[DNS ゾーンとレコードの概要](dns-zones-records.md)」でDNS ゾーンとレコードについて学びます。

Azure のその他の重要な[ネットワーク機能](../networking/fundamentals/networking-overview.md)について参照してください。
