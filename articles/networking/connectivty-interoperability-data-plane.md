---
title: 'ExpressRoute、サイト間 VPN、VNET ピアリングの相互運用性 - データ プレーン分析: Azure バックエンド接続機能の相互運用性 | Microsoft Docs'
description: このページでは、ExpressRoute、サイト間 VPN、VNET ピアリングの機能の相互運用性を分析するために構築されたテスト構成のデータ プレーン分析について説明します｡
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: c9f3824b1e0f44338696ba3c2e434d60eee3af8b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947102"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---data-plane-analysis"></a>ExpressRoute、サイト間 VPN、VNET ピアリングの相互運用性 - データ プレーン分析

この記事では､テスト構成のデータ プレーン分析を一通り見てみます｡ テスト構成については､[テスト構成][Setup]を参照してください｡ テスト構成の構成詳細については､｢[テスト構成の構成][Configuration]｣を参照してください｡ テスト構成のコントロール プレーンの分析を確認するには、[コントロール プレーンの分析][Control-Analysis]を参照してください。

データ プレーンの分析では、あるトポロジ内の 1 つのローカル ネットワーク (LAN/VNet) から別のローカル ネットワークへと通過するパケットで使用されるパスについて調べます。 2 つのローカル ネットワーク間のデータ パスは、必ずしも対称でない場合があります。 したがって、この記事ではローカル ネットワークから別のネットワークへの転送パスを、逆方向パスから個別に分析してみます。

##<a name="data-path-from-hub-vnet"></a>ハブ VNet からのデータ パス

###<a name="path-to-spoke-vnet"></a>スポーク VNet へのパス

VNet ピアリングは、ピアリングされている 2 つの VNet 間のネットワーク ブリッジ機能をエミュレートします。 ハブ VNet からスポーク VNet 内の VM への Traceroute 出力を以下に示します。

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

次の画面クリップは、Azure Network Watcher によって表示される、ハブ VNet とスポーク VNet のグラフィカル接続ビューです。


[![1]][1]

###<a name="path-to-branch-vnet"></a>分岐 VNet へのパス

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

上記の traceroute では、最初のホップはハブ VNet の VPN GW です。 2 番目のホップは分岐 VNet の VPN GW で、その IP アドレスはハブ VNet 内でアドバタイズされません。 3 番目のホップは、分岐 VNet 上の VM です。

次の画面クリップは、Azure Network Watcher によって表示される、ハブ VNet と分岐 VNet のグラフィカル接続ビューです。

[![2]][2]

同じ接続について、次の画面クリップは Azure Network Watcher で表示されるグリッド ビューです。

[![3]][3]

###<a name="path-to-on-premises-location-1"></a>オンプレミスの場所 1 へのパス

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

上記の traceroute で、最初のホップは、MSEE への ExpressRoute GW トンネル エンドポイントです。 2 番目と 3 番目のホップはそれぞれ CE ルーターおよびオンプレミスの場所 1 の LAN IP で、これらの IP アドレスはハブ VNet 内でアドバタイズされません。 4 番目のホップは、オンプレミスの場所 1 の VM です。


###<a name="path-to-on-premises-location-2"></a>オンプレミスの場所 2 へのパス

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

上記の traceroute で、最初のホップは、MSEE への ExpressRoute GW トンネル エンドポイントです。 2 番目と 3 番目のホップはそれぞれ CE ルーターおよびオンプレミスの場所 2 の LAN IP で、これらの IP アドレスはハブ VNet 内でアドバタイズされません。 4 番目のホップは、オンプレミスの場所 2 の VM です。

###<a name="path-to-remote-vnet"></a>リモート VNet へのパス

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

上記の traceroute で、最初のホップは、MSEE への ExpressRoute GW トンネル エンドポイントです。 2 番目のホップは、リモート VNet の GW IP です。 2 番目のホップの IP 範囲はハブ VNet 内でアドバタイズされません。 3 番目のホップは、リモート VNet 上の VM です。

##<a name="data-path-from-spoke-vnet"></a>スポーク VNet からのデータ パス

スポーク VNet はハブ VNet のネットワーク ビューを共有していることを思い出してください。 VNet ピアリングによって、スポーク VNet は、ハブ VNet のリモート ゲートウェイ接続をそれらがスポーク VNet に直接接続されているかのように使用します。

###<a name="path-to-hub-vnet"></a>ハブ VNet へのパス

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

###<a name="path-to-branch-vnet"></a>分岐 VNet へのパス

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

上記の traceroute では、最初のホップはハブ VNet の VPN GW です。 2 番目のホップは分岐 VNet の VPN GW で、その IP アドレスはハブ/スポーク VNet 内でアドバタイズされません。 3 番目のホップは、分岐 VNet 上の VM です。

###<a name="path-to-on-premises-location-1"></a>オンプレミスの場所 1 へのパス

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

上記の traceroute で、最初のホップは、MSEE へのハブ VNet の ExpressRoute GW トンネル エンドポイントです。 2 番目と 3 番目のホップはそれぞれ CE ルーターおよびオンプレミスの場所 1 の LAN IP で、これらの IP アドレスはハブ/スポーク VNet 内でアドバタイズされません。 4 番目のホップは、オンプレミスの場所 1 の VM です。

###<a name="path-to-on-premises-location-2"></a>オンプレミスの場所 2 へのパス

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

上記の traceroute で、最初のホップは、MSEE へのハブ VNet の ExpressRoute GW トンネル エンドポイントです。 2 番目と 3 番目のホップはそれぞれ CE ルーターおよびオンプレミスの場所 2 の LAN IP で、これらの IP アドレスはハブ/スポーク VNet 内でアドバタイズされません。 4 番目のホップは、オンプレミスの場所 2 の VM です。

###<a name="path-to-remote-vnet"></a>リモート VNet へのパス

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

上記の traceroute で、最初のホップは、MSEE へのハブ VNet の ExpressRoute GW トンネル エンドポイントです。 2 番目のホップは、リモート VNet の GW IP です。 2 番目のホップの IP 範囲はハブ/スポーク VNet 内でアドバタイズされません。 3 番目のホップは、リモート VNet 上の VM です。

##<a name="data-path-from-branch-vnet"></a>分岐の VNet からのデータ パス

###<a name="path-to-hub-vnet"></a>ハブ VNet へのパス

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

上記の traceroute では、最初のホップは分岐 VNet の VPN GW です。 2 番目のホップはハブ VNet の VPN GW で、その IP アドレスはリモート VNet 内でアドバタイズされません。 3 番目のホップは、ハブ VNet 上の VM です。

###<a name="path-to-spoke-vnet"></a>スポーク VNet へのパス

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

上記の traceroute では、最初のホップは分岐 VNet の VPN GW です。 2 番目のホップはハブ VNet の VPN GW で、その IP アドレスはリモート VNet 内でアドバタイズされず、3 番目のホップはスポーク VNet 上の VM で。

###<a name="path-to-on-premises-location-1"></a>オンプレミスの場所 1 へのパス

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

上記の traceroute では、最初のホップは分岐 VNet の VPN GW です。 2 番目のホップはハブ VNet の VPN GW で、その IP アドレスはリモート VNet 内でアドバタイズされません。 3 番目のホップは、プライマリ CE ルーター上の VPN トンネルの終了ポイントです。 4 番目のホップは、CE ルーターからアドバタイズされないオンプレミスの場所 1 の LAN IP アドレスの内部 IP アドレスです。 5 番目のホップは、オンプレミスの場所 1 の宛先 VM です。

###<a name="path-to-on-premises-location-2-and-remote-vnet"></a>オンプレミスの場所 2 およびリモート VNet へのパス

コントロール プレーンの分析で以前説明したように、分岐 VNet はネットワーク構成に従い、オンプレミスの場所 2 およびリモート VNet への可視性がありません。 次の ping の結果によって事実を確認します。 

    C:\Users\rb>ping 10.1.31.10

    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\Users\rb>ping 10.17.30.4

    Pinging 10.17.30.4 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.17.30.4:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

##<a name="data-path-from-on-premises-location-1"></a>オンプレミスの場所 1 からのデータ パス

###<a name="path-to-hub-vnet"></a>ハブ VNet へのパス

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

上記の traceroute で、最初の 2 つのホップはオンプレミス ネットワークの一部です。 3 番目のホップは、CE ルーターに接続するプライマリ MSEE インターフェイスです。 4 番目のホップは、ハブ VNet の ExpressRoute G/W で、その IP 範囲はオンプレミス ネットワークにアドバタイズされません。 5 番目のホップは宛先 VM です。

Azure Network Watcher では、Azure を中心としたビューのみを提供します。 そのため、オンプレミス中心のビューでは Azure Network Performance Monitor (NPM) を使用しました。 NPM では、Azure の外部ネットワークにあるサーバーにエージェントをインストールして、データ パスの分析を行うことができます。

次の画面クリップは、オンプレミスの場所 1 の VM から ExpressRoute 経由でハブ VNet の VM に接続したトポロジ ビューを示します。

[![4]][4]

テスト セットアップでは、サイト間 VPN をオンプレミスの場所 1 とハブ VNet 間の ExpressRoute 接続のバックアップ接続として使用したことを思い出してください。 データパスをテストするために、MSEE に接続する CE インターフェイスをシャットダウンすることによって、オンプレミスの場所 1 のプライマリ CE ルーターと、対応する MSEE の間で ExpressRoute リンク障害を発生させてみましょう。

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

次の画面クリップは、ExpressRoute 接続がダウンした場合にオンプレミスの場所 1 の VM からサイト間 VPN 接続経由でハブ VNet の VM に接続したトポロジ ビューを示します。

[![5]][5]

###<a name="path-to-spoke-vnet"></a>スポーク VNet へのパス

スポーク VNet に対するデータパス分析を行うために ExpressRoute のプライマリ接続を元に戻します。

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

残りのデータパス分析のために ExpressRoute-1 のプライマリ接続を起動します。

###<a name="path-to-branch-vnet"></a>分岐 VNet へのパス

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

###<a name="path-to-on-premises-location-2"></a>オンプレミスの場所 2 へのパス

コントロール プレーンの分析で以前説明したように、オンプレミスの場所 1 はネットワーク構成に従い、オンプレミスの場所 2 への可視性がありません。 次の ping の結果によって事実を確認します。 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

###<a name="path-to-remote-vnet"></a>リモート VNet へのパス

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

##<a name="data-path-from-on-premises-location-2"></a>オンプレミスの場所 2 からのデータ パス

###<a name="path-to-hub-vnet"></a>ハブ VNet へのパス

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>スポーク VNet へのパス

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

###<a name="path-to-branch-vnet-on-premises-location-1-and-remote-vnet"></a>分岐 VNet、オンプレミスの場所 1、およびリモート VNet へのパス

コントロール プレーンの分析で以前説明したように、オンプレミスの場所 1 はネットワーク構成に従い、分岐 VNet、オンプレミスの場所 1、およびリモート VNet への可視性がありません。 

##<a name="data-path-from-remote-vnet"></a>リモート VNet からのデータ パス

###<a name="path-to-hub-vnet"></a>ハブ VNet へのパス

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>スポーク VNet へのパス

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-branch-vnet-and-on-premises-location-2"></a>分岐 VNet およびオンプレミスの場所 2 へのパス

コントロール プレーンの分析で以前説明したように、リモート VNet はネットワーク構成に従い、分岐 VNet およびオンプレミスの場所 2 への可視性がありません。 


### <a name="path-to-on-premises-location-1"></a>オンプレミスの場所 1 へのパス

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="further-reading"></a>参考情報

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute とサイト間 VPN 接続を組み合わせて使用する

####<a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute 上のサイト間 VPN

ExpressRoute Microsoft ピアリング上にサイト間 VPN を構成することにより、オンプレミス ネットワークと Azure VNet との間で、機密性とアンチリプレイ、信頼性、整合性を確保しながらプライベートにデータを交換することができます。 ExpressRoute Microsoft ピアリング上にトンネル モードでサイト間 IPsec VPN を構成する方法についての詳細は、[ExpressRoute Microsoft ピアリング上にサイト間 VPNを構成する方法][S2S-Over-ExR]に関するページを参照してください。 

Microsoft ピアリング上に S2S VPN を構成することに伴う重要な制限はスループットです。 IPsec トンネル上では、VPN GW の容量によりスループットが制限されます。 VPN GW のスループットは、ExpressRoute のスループットよりも低下します｡ そのようなシナリオでは、高いセキュリティが要求されるトラフィックには IPsec トンネルを使用し、それ以外のトラフィックにはすべてプライベート ピアリングを使用することで、ExpressRoute の帯域幅使用率を最適化することができるでしょう。

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute のセキュアなフェールオーバー パスとしてのサイト間 VPN
ExpressRoute は、高可用性を確保するための冗長回線ペアとして提供されます。 複数の Azure リージョンに geo 冗長の ExpressRoute 接続を構成することができます。 また、ここで紹介したテスト構成のように、特定の Azure リージョン内で ExpressRoute 接続のフェールオーバー パスを設けたければ、サイト間 VPN を使用することができます。 ExpressRoute と S2S VPN の両方に同じプレフィックスがアドバタイズされた場合、ExpressRoute は S2S VPN よりも優先されます。 ExpressRoute と S2S VPN との間で非対称なルーティングを回避するために、オンプレミス ネットワーク構成でも、ExpressRoute を S2S VPN 接続よりも優先するように構成する必要があります。

ExpressRoute とサイト間 VPN が共存する接続を構成する方法についての詳細は、[ExpressRoute とサイト間の共存接続][ExR-S2S-CoEx]に関するページを参照してください。

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>スポーク VNET と分岐先へのバックエンド接続の延長

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>VNET ピアリングを使用したスポーク VNET 接続

Hub-and-spoke VNET アーキテクチャは広く利用されています｡ Azure ではこのハブは仮想ネットワーク (VNet) であり､オンプレミスネットワークに接続しているスポーク VNET 間の接続の中心点の働きをします｡ スポークは、ハブと相互接続された VNet であり、ワークロードを分離する目的で使用できます。 オンプレミスのデータセンターとハブとの間のトラフィックは、ExpressRoute または VPN 接続を経由して送信されます。 このアーキテクチャの詳細については、[ハブ アンド スポーク アーキテクチャ][Hub-n-Spoke]に関するページを参照してください。

スポーク VNet は、同じリージョン内の VNet ピアリングを通じてハブ VNet ゲートウェイ (VPN ゲートウェイと ExpressRoute ゲートウェイの両方) を使用し、リモート ネットワークと通信を行うことができます。

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>サイト間 VPN を使用した分岐 VNet 接続

分岐 VNet (異なるリージョンに存在) とオンプレミス ネットワークとをハブ VNet 経由で互いに通信させたい場合、Azure のネイティブ ソリューションは VPN を使用したサイト間 VPN 接続になります。 ハブにおけるルーティングには NVA を使用する選択肢もあります。

VPN ゲートウェイの構成については､[VPN Gateway の構成][VPN]に関するページを参照してください。 高可用性の NVA のデプロイについては､[高可用性のネットワーク仮想アプライアンスをデプロイする][Deploy-NVA]を参照してください。

## <a name="next-steps"></a>次の手順

ExpressRoute ゲートウェイに接続できる ExpressRoute 回線の数や、ExpressRoute 回線に接続できる ExpressRoute ゲートウェイの数など、ExpressRoute のスケーリングに関するその他の制限については、[ExpressRoute FAQ][ExR-FAQ] を参照してください


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "ハブ VNet からスポーク VNet への接続の Network Watcher ビュー"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "ハブ VNet から分岐 VNet への接続の Network Watcher ビュー"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "ハブ VNet から分岐 VNet への接続の Network Watcher グリッド ビュー"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "場所 1 の VM から ExpressRoute 1 経由でハブ VNet に接続するときの Network Performance Monitor ビュー"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "場所 1 の VM から S2S VPN 経由でハブ VNet に接続するときの Network Performance Monitor ビュー"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering




