---
title: Azure 仮想ネットワークに関する FAQ | Microsoft Docs
description: Microsoft Azure 仮想ネットワークについてよく寄せられる質問に回答します。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 54bee086-a8a5-4312-9866-19a1fba913d0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: jdial
ms.openlocfilehash: 8dfe313cb82fd0ace7221ea320bb2228be75196c
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038636"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Azure 仮想ネットワークについてよく寄せられる質問 (FAQ)

## <a name="virtual-network-basics"></a>仮想ネットワークの基礎

### <a name="what-is-an-azure-virtual-network-vnet"></a>Azure 仮想ネットワーク (VNet) とは何ですか。
Azure 仮想ネットワーク (VNet) は、クラウド内のユーザー独自のネットワークを表すものです。 サブスクリプション専用に Azure クラウドが論理的に分離されています。 VNet を使用してプロビジョニングして Azure で仮想プライベート ネットワーク (VPN) を管理および、必要に応じて、VNet を Azure で他の VNet とリンクさせたり、オンプレミス IT インフラストラクチャで、ハイブリッドまたはクロスプレミス ソリューションを作成します。 作成したそれぞれの VNet には独自の CIDR ブロックがあり、CIDR ブロックが競合しない限り、他の VNet やオンプレミス ネットワークにリンクすることができます。 VNet の DNS サーバー設定と、サブネットへの VNet の分割も制御できます。

VNet を使用して次のことが行えます。

* プライベート クラウドのみの専用 VNet を作成します。ソリューションがクロスプレミス構成が必要ない場合もあります。 VNet を作成するとき、ご使用のサービスと VNet 内の VMが、クラウド内で互いに直接かつ安全に通信することができます。 VM と、ソリューションの一部としてインターネット通信を必要とするサービスとのエンドポイント接続を構成することができます。
* データ センターを安全に拡張します。VNet を使用すると、従来のサイト間 (S2S) VPN を構築して、データセンターの容量を安全に拡張できます。 S2S VPN は、IPSEC を使用して、社内の VPN ゲートウェイと Azure 間の安全な接続を提供します。
* ハイブリッド クラウドのシナリオを有効にします。VNet は、さまざまなハイブリッド クラウド シナリオをサポートする柔軟性を提供します。 メインフレームなどのオンプレミス システムと Unix システムの任意の型へのクラウド ベースのアプリケーションを安全に接続することができます。

### <a name="how-do-i-get-started"></a>開始するには?
「[Virtual Network のドキュメント](https://docs.microsoft.com/azure/virtual-network/)」にアクセスし、開始します。 このコンテンツには、すべての VNet 機能の概要とデプロイに関する情報が示されています。

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>クロスプレミス接続を使用しないで VNet を使用できますか。
はい。 プレミスに接続せずに VNet を使用することができます。 たとえば、Microsoft Windows Server Active Directory ドメイン コントローラーと SharePoint ファームを Azure VNet 内のみで実行できます。

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>VNet 間または VNet とオンプレミスのデータ センター間で WAN の最適化を実行することはできますか。
はい。 Azure Marketplace を通じて複数のベンダーから提供されている [WAN 最適化ネットワーク仮想アプライアンス](https://azure.microsoft.com/marketplace/?term=wan+optimization)をデプロイできます。

## <a name="configuration"></a>構成

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>VNet を作成するのには、どのようなツールを使用でしょうか。
次のツールを使用して、VNet を作成または構成できます。

* Azure ポータル
* PowerShell
* Azure CLI
* ネットワーク構成ファイル (netcfg - クラシック VNet のみ)。 「[Configure a VNet using a network configuration file (ネットワーク構成ファイルを使用した VNet の構成)](virtual-networks-using-network-configuration-file.md)」を参照してください。

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>VNet でどのアドレス範囲が使用できるでしょうか。
[RFC 1918](http://tools.ietf.org/html/rfc1918) で定義されている任意の IP アドレス範囲 (例: 10.0.0.0/16)。

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>VNet 内でパブリック IP アドレスを持つことができますか。
はい。 パブリック IP アドレス範囲の詳細については、[仮想ネットワークの作成](manage-virtual-network.md#create-a-virtual-network)に関する記事をご覧ください。 パブリック IP アドレスは、インターネットから直接アクセスできません。

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>VNet 内のサブネットの数に制限はありますか。
はい。 詳細については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)に関する記事をご覧ください。 サブネットのアドレス空間は、互いに重複することはできません。

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>これらのサブネット内の IP アドレスの使用に関する制限はありますか。
はい。 Azure は、各サブネット内で一部の IP アドレスを予約します。 各サブネットの最初と最後の IP アドレスは、Azure サービスに使用される各サブネットの x.x.x.1 ～ x.x.x.3 アドレスと共に、プロトコル準拠のために予約されます。

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>VNet およびサブネットは、どれくらい小規模に、また、大規模になるのでしょうか。
サポートされる最小のサブネットは /29、最大は /8 です (CIDR サブネット定義を使用)。

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>VNet を使用して、VLAN を Azure に接続できるでしょうか。
いいえ。 VNet はレイヤー 3 のオーバーレイです。 Azure では、任意のレイヤー 2 のセマンティクスはサポートされません。

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>私の Vnet とサブネットをカスタム ルーティング ポリシーを指定できますか。
はい。 ルート テーブルを作成してサブネットに関連付けることができます。 Azure でのルーティングの詳細については、[ルーティングの概要](virtual-networks-udr-overview.md#custom-routes)に関する記事をご覧ください。

### <a name="do-vnets-support-multicast-or-broadcast"></a>VNet はマルチキャストやブロードキャストをサポートしますか。
いいえ。 マルチキャストとブロードキャストはサポートされていません。

### <a name="what-protocols-can-i-use-within-vnets"></a>VNet 内はどのようなプロトコルを使用できますか。
VNet では、TCP、UDP、および ICMP TCP/IP プロトコルを使用することができます。 ユニキャストは VNet 内でサポートされますが、ユニキャスト (ソース ポート UDP/68 / 宛先ポート UDP/67) を経由した動的ホスト構成プロトコル (DHCP) は例外です。 マルチキャスト、ブロードキャスト、IP-in-IP のカプセル化されたパケット、Generic Routing Encapsulation (GRE) のパケットは、VNet 内でブロックされます。 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>VNet 内には、既定ルーターを ping できるでしょうか。
いいえ。

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Tracert を使用して、接続を診断することができますか。
いいえ。

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>VNet を作成した後のサブネットを追加できますか。
はい。 サブネットのアドレス範囲が VNet 内の別のサブネットの一部でなく、仮想ネットワークのアドレス範囲に空き領域があれば、いつでもサブネットを VNet に追加できます。

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>作成した後、サブネットのサイズを変更できますか。
はい。 VM またはサービスがサブネット内にデプロイされていない場合は、サブネットを追加、削除、拡張、または縮小できます。

### <a name="can-i-modify-subnets-after-i-created-them"></a>サブネットを作成後に変更できますか。
はい。 VNet で使用された CIDR ブロックを追加、削除、変更することができます。

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>VNet でサービスを実行している場合、インターネットに接続できますか。
はい。 VNet 内にデプロイされているすべてのサービスは、インターネットに送信接続できます。 Azure での送信インターネット接続について詳しくは、[送信接続](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。 Resource Manager を使用してデプロイされたリソースに受信接続するには、リソースにパブリック IP アドレスが割り当てられている必要があります。 パブリック IP アドレスについて詳しくは、[パブリック IP アドレス](virtual-network-public-ip-address.md)に関する記事をご覧ください。 Azure にデプロイされたすべての Azure クラウド サービスには、パブリックにアドレス指定可能な VIP が割り当てられています。 PaaS ロールの入力エンドポイントと仮想マシンのエンドポイントを定義して、これらのサービスがインターネットからの接続を承諾できるようにします。

### <a name="do-vnets-support-ipv6"></a>VNet は IPv6 をサポートするでしょうか。
いいえ。 この時点で、VNet で IPv6 を使用できません。 ただし、Azure ロード バランサーに IPv6 アドレスを割り当てて、仮想マシンを負荷分散することはできます。 詳細については、「[Azure Load Balancer の IPv6 の概要](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。

### <a name="can-a-vnet-span-regions"></a>VNet は複数のリージョンで広がるでしょうか。
いいえ。 VNet は、1 つのリージョンに制限されます。 ただし、仮想ネットワークは複数の可用性ゾーンにまたがります。 可用性ゾーンの詳細については、「[可用性ゾーンの概要](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 仮想ネットワーク ピアリングを使用して、別々のリージョンにある仮想ネットワークを接続できます。 詳細については、[仮想ネットワーク ピアリングの概要](virtual-network-peering-overview.md)に関する記事をご覧ください。

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>VNet を Azure での別の VNet に接続できますか。
はい。 次のいずれかの方法で VNet どうしを接続できます。
- **仮想ネットワーク ピアリング**: 詳細については、[VNet ピアリングの概要](virtual-network-peering-overview.md)に関する記事をご覧ください。
- **Azure VPN Gateway**: 詳細については、[VNet 間接続の構成](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。 

## <a name="name-resolution-dns"></a>名前解決 (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>VNet の DNS オプションとは何でしょうか。
[VM とロール インスタンスの名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md) ページでディシジョン テーブルを使用して、使用できるすべての DNS オプションを行うことができます。

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>VNet の DNS サーバーを指定できますか。
はい。 VNet 設定では、DNS サーバーの IP アドレスを指定できます。 設定は、VNet 内のすべての VM の既定の DNS サーバーとして適用されます。

### <a name="how-many-dns-servers-can-i-specify"></a>DNS サーバーの数を指定できますか。
[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)に関する記事をご覧ください。

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>ネットワークを作成した後、DNS サーバーを変更できますか。
はい。 いつでも、VNet の DNS サーバーの一覧を変更できます。 DNS サーバーの一覧を変更する場合は、新しい DNS サーバーを取得するために VNet で VM をそれぞれ再起動する必要があります。

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Azure で提供される DNS と Vnet と組み合わせて使用について
Azure で提供される DNS は、Microsoft によって提供されるマルチ テナント DNS サービスです。 Azure は、すべての VM とクラウド サービス ロール インスタンスをこのサービスに登録します。 このサービスは、同じクラウド サービス内に含まれる VM とロール インスタンスのホスト名によって、また、同じ VNet 内の VM とロール インスタンスの FQDN によって名前解決を提供します。 DNS の詳細については、[VM と Cloud Services ロール インスタンスの名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md)に関する記事をご覧ください。

Azure で提供される DNS を使用したテナント間名前解決は、VNet の最初の 100 個のクラウド サービスまでに制限されます。 独自の DNS サーバーを使用している場合は、この制限は適用されません。

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>VM またはクラウド サービスごとに、DNS 設定をオーバーライドできますか。
はい。 VM またはクラウド サービスごとに DNS サーバーを設定し、既定のネットワーク設定をオーバーライドすることができます。 ただし、できるだけネットワーク全体の DNS を使用することをお勧めします。

### <a name="can-i-bring-my-own-dns-suffix"></a>独自の DNS サフィックスを取り込むことができますか。
いいえ。 ご使用の VNet のカスタム DNS サフィックスを指定することはできません。

## <a name="connecting-virtual-machines"></a>仮想マシンの接続

### <a name="can-i-deploy-vms-to-a-vnet"></a>VNet に VM をデプロイできますか。
はい。 Resource Manager デプロイ モデルを使用してデプロイされた VM に接続されているすべてのネットワーク インターフェイス (NIC) は、VNet に接続されている必要があります。 クラシック デプロイ モデルを使用してデプロイされた VM は、必要に応じて VNet に接続できます。

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>VM に割り当てることができる IP アドレスにはどのような種類がありますか。
* **プライベート:** 各 VM 内の各 NIC に割り当てられます。 アドレスは、静的または動的な方法を使用して割り当てられます。 プライベート IP アドレスは、VNet のサブネット設定で指定した範囲から割り当てられます。 クラシック デプロイ モデルを使用してデプロイされたリソースは、VNet に接続されていなくてもプライベート IP アドレスが割り当てられます。 割り当て方法の動作は、リソースが Resource Manager デプロイ モデルとクラシック デプロイ モデルのどちらを使用してデプロイされたかによって異なります。 

  - **Resource Manager**: 動的または静的な方法を使用して割り当てられたプライベート IP アドレスは、リソースが削除されるまで、仮想マシン (Resource Manager) に割り当てられたままになります。 異なるのは、静的な方法の場合はユーザーが割り当てるアドレスを選択し、動的な方法の場合は Azure が選択するという点です。 
  - **クラシック**: 動的な方法を使用して割り当てられたプライベート IP アドレスは、仮想マシン (クラシック) VM が停止 (割り当て解除) 状態になった後で再起動されたときに変更される可能性があります。 クラシック デプロイ モデルを使用してデプロイされるリソースのプライベート IP アドレスを固定する必要がある場合は、静的な方法を使用してプライベート IP アドレスを割り当ててください。

* **パブリック:** 必要に応じて、Azure Resource Manager デプロイ モデルを使用してデプロイされた VM に接続されている NIC に割り当てられます。 アドレスは、静的または動的な割り当て方法を使用して割り当てることができます。 クラシック デプロイ モデルを使用してデプロイされたすべての VM および Cloud Services ロール インスタンスは、"*動的な*" パブリック仮想 IP (VIP) アドレスが割り当てられたクラウド サービス内に存在します。 パブリックな "*静的*" IP アドレスは、[予約済み IP アドレス](virtual-networks-reserved-public-ip.md)と呼ばれ、必要に応じて VIP として割り当てることができます。 パブリック IP アドレスは、クラシック デプロイ モデルを使用してデプロイされた個々の VM または Cloud Services ロール インスタンスに割り当てることができます。 これらのアドレスは、[インスタンス レベルのパブリック IP (ILPIP)](virtual-networks-instance-level-public-ip.md) アドレスと呼ばれ、動的に割り当てることができます。

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>後で作成する VM 用にプライベート IP アドレスを予約することはできますか。
いいえ。 プライベート IP アドレスを予約することはできません。 プライベート IP アドレスが使用可能な場合は、DHCP サーバーによって VM またはロール インスタンスに割り当てられます。 その VM は、プライベート IP アドレスを割り当てたい VM である場合も、そうでない場合もあります。 ただし、既に作成した VM のプライベート IP アドレスを、使用可能なプライベート IP アドレスに変更することができます。

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>VNet 内の VM のプライベート IP アドレスは変更されますか。
一概には言えません。 VM が Resource Manager を使用してデプロイされた場合は、静的または動的な割り当て方法のどちらを使用して割り当てられていても、変更されることはありません。 VM がクラシック デプロイ モデルを使用してデプロイされた場合は、VM が停止 (割り当て解除) 状態になった後で起動された場合に、動的 IP アドレスが変更される可能性があります。 このアドレスは、いずれかのデプロイメント モデルを使用してデプロイされた VM が削除されると、その VM から解放されます。

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>VM オペレーティング システム内の NIC に手動で IP アドレスを割り当てることはできますか。
はい。ただし、必要な場合 (仮想マシンに複数の IP アドレスを割り当てる場合など) 以外はお勧めしません。 詳細については、[仮想マシンに複数の IP アドレスを追加する方法](virtual-network-multiple-ip-addresses-portal.md#os-config)に関する記事をご覧ください。 VM にアタッチされた Azure NIC に割り当てられている IP アドレスが変更され、VM オペレーティング システム内の IP アドレスと異なる場合は、VM への接続が失われます。

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>クラウド サービス デプロイ スロットを停止した場合や、オペレーティング システム内から VM をシャットダウンした場合、IP アドレスはどうなりますか。
何もありません。 IP アドレス (パブリック VIP、パブリック、プライベート) は、クラウド サービス デプロイ スロットまたは VM に割り当てられたままとなります。

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>VNet 内の別のサブネットへ、再デプロイせずに移動できますか。
はい。 詳細については、「[VM またはロール インスタンスを別のサブネットに移動する方法](virtual-networks-move-vm-role-to-subnet.md)」を参照してください。

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>VM に静的な MAC アドレスを構成できますか。
いいえ。 MAC アドレスを静的に構成することはできません。

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>MAC アドレスは、一度作成されると、VM で同じものとして残りますか。
はい。MAC アドレスは、Resource Manager デプロイ モデルを使用してデプロイされた VM とクラシック デプロイ モデルを使用してデプロイされた VM のどちらの場合も、削除されるまで同じままです。 以前は、VM が停止 (割り当て解除) された場合に MAC アドレスが解放されました。現在、MAC アドレスは、VM が割り当て解除状態であっても保持されます。

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>VNet 内の VM からインターネットに接続できますか。
はい。 VNet 内にデプロイされているすべての VM および Cloud Services ロール インスタンスは、インターネットに接続できます。

## <a name="azure-services-that-connect-to-vnets"></a>VNet に接続する Azure サービス

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Azure App Service Web Apps を VNet で使用することはできますか。
はい。 ASE (App Service Environment) を使用して VNet 内に Web Apps をデプロイできます。 VNet にポイント対サイト接続が構成されている場合、すべての Web Apps は、VNet 内のリソースに安全に接続およびアクセスできます。 詳細については、次の記事を参照してください。

* [App Service 環境で Web Apps を作成する](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [アプリを Azure 仮想ネットワークに統合する](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Web Apps と VNet Integration and Hybrid Connections の併用](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Web ロールと worker ロール (PaaS) を持つ Cloud Services を VNet にデプロイすることはできますか。
はい。 Cloud Services ロール インスタンスは、VNet 内に (オプションで) デプロイできます。 そのためには、サービス構成のネットワーク構成セクションで、VNet の名前と、ロールとサブネットのマッピングを指定します。 どのバイナリも更新する必要はありません。

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>仮想マシン スケール セット (VMSS) を VNet に接続することはできますか。
はい。 VMSS は VNet に接続する必要があります。

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>VNet 内にリソースをデプロイできる Azure サービスの完全な一覧はありますか。
はい。詳細については、「[Azure サービスの仮想ネットワーク統合](virtual-network-for-azure-services.md)」をご覧ください。

### <a name="which-azure-paas-resources-can-i-restrict-access-to-from-a-vnet"></a>どの Azure PaaS リソースで、VNet からのアクセスのみにアクセスを制限できますか。

Azure Storage、Azure SQL Database などの一部の Azure PaaS サービスを使用してデプロイされたリソースは、仮想ネットワーク サービス エンドポイントの使用によって、VNet 内のリソースのみにネットワーク アクセスを制限できます。 詳細については、[仮想ネットワーク サービス エンドポイントの概要](virtual-network-service-endpoints-overview.md)に関する記事をご覧ください。

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>サービスを VNet 内外で移動できますか。
いいえ。 サービスを VNet 内外で移動することはできません。 リソースを別の VNet に移動するには、リソースを削除して再デプロイする必要があります。

## <a name="security"></a>セキュリティ

### <a name="what-is-the-security-model-for-vnets"></a>VNet のセキュリティ モデルとは何ですか。
Vnet は、他の VNet から、および Azure インフラストラクチャでホストされている他のサービスから、完全に分離されています。 VNet は、トラスト バウンダリです。

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>受信または送信トラフィック フローを VNet に接続されたリソースに制限することはできますか。
はい。 VNet 内の個々のサブネット、VNet に接続された NIC、またはその両方に[ネットワーク セキュリティ グループ](security-overview.md)を適用できます。

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>VNet に接続されたリソースの間にファイアウォールを実装することはできますか。
はい。 Azure Marketplace を通じて複数のベンダーから提供されている[ファイアウォール ネットワーク仮想アプライアンス](https://azure.microsoft.com/marketplace/?term=firewall)をデプロイできます。

### <a name="is-there-information-available-about-securing-vnets"></a>VNet のセキュリティ保護に関する情報はありますか。
はい。 詳細については、「[Azure のネットワーク セキュリティの概要](../security/security-network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。

## <a name="apis-schemas-and-tools"></a>API、スキーマ、およびツール

### <a name="can-i-manage-vnets-from-code"></a>VNet をコードから管理できますか。
はい。 [Azure Resource Manager](/rest/api/virtual-network) デプロイメント モデルおよび[クラシック (Service Management)](http://go.microsoft.com/fwlink/?LinkId=296833) デプロイメント モデルの VNet に REST API を使用できます。

### <a name="is-there-tooling-support-for-vnets"></a>VNet に対するツール サポートはありますか。
はい。 次のツールを使用できます。
- Azure Portal。[Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) および[クラシック](virtual-networks-create-vnet-classic-pportal.md) デプロイメント モデルを使用して VNet をデプロイするために使用します。
- PowerShell。[Resource Manager](/powershell/module/azurerm.network) および[クラシック](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) デプロイメント モデルを使用してデプロイされた VNet を管理するために使用します。
- Azure コマンド ライン インターフェイス (CLI)。[Resource Manager](/cli/azure/network/vnet) デプロイメント モデルおよび[クラシック](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources) デプロイメント モデルを使用してデプロイされた VNet をデプロイおよび管理するために使用します。  

## <a name="vnet-peering"></a>VNET ピアリング

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>別のリージョンの VNet へのピアリング接続を作成できますか。
はい。 グローバル VNet ピアリングを使用すると、別のリージョンの VNet にピアリングできます。 グローバル VNet ピアリングは、Azure のすべてのパブリック リージョンで使用できます。 Azure のパブリック リージョンからナショナル クラウドにグローバルにピアリングすることはできません。 現在、グローバル ピアリングはナショナル クラウドでは使用できません。

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>仮想ネットワークが別の Azure Active Directory テナント内のサブスクリプションに属する場合、VNet ピアリングを有効にできますか。
現在、サブスクリプションが別の Azure Active Directory テナントに属する場合、(ローカルまたはグローバルのどちらでも) VNet ピアリング を確立することはできません。

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>VNet ピアリング接続が*開始済み*の状態にあります。接続できないのはなぜですか。
ピアリング接続が開始済みの状態にある場合、これは、リンクを 1 つだけ作成していることを意味します。 正常な接続を確立するには、双方向のリンクを作成する必要があります。 VNet A から VNet B にピアリングするには、VNetA から VNetB、および VNetB から VNetAへのリンクを作成する必要があります。 両方のリンクを作成すると、状態が*接続済み*に変更されます。

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>VNet を別のサブスクリプションにある VNet とピアリングできますか。
はい。 VNet はサブスクリプションやリージョンを越えてピアリングできます。

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>一致するまたは重複するアドレス範囲にある 2 つの VNet をピアリングできますか。
いいえ。 VNet ピアリングを有効にするには、アドレス空間がオーバーラップしない必要があります。

### <a name="how-much-do-vnet-peering-links-cost"></a>VNet ピアリング リンクの料金をどれくらいですか。
VNet ピアリング接続を作成するのに料金はかかりません。 ピアリング接続経由でのデータ転送には料金が発生します。 [こちらを参照](https://azure.microsoft.com/pricing/details/virtual-network/)してください。

### <a name="is-vnet-peering-traffic-encrypted"></a>VNet ピアリングのトラフィックは暗号化されますか。
いいえ。 ピアリングされた VNet のリソース間のトラフィックはプライベートで分離されています。 マイクロソフトのバックボーンに完全に残ります。

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>ピアリング接続が切断状態にあるのはなぜですか。
1 つの VNet ピアリング リンクが削除されると、VNet ピアリングは*切断*状態になります。 ピアリング接続を再度確立するには、両方のリンクを削除する必要があります。

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>VNetA から VNetB にピアリングし、VNetB から VNetC にピアリングすると、VNetA と VNetC がピアリングされたことになりますか。
いいえ。 推移的なピアリングはサポートされません。 VNetA と VNetC をピアリングする必要があります。

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>ピアリング接続に帯域幅制限はありますか。
いいえ。 ローカルであってもグローバルであっても、VNet ピアリングで帯域幅制限は課されません。 帯域幅は VM やコンピューティング リソースによってのみ制限されます。

