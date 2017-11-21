---
title: "Azure の送信用接続の詳細 | Microsoft Docs"
description: "この記事では、Azure によって、パブリック インターネット サービスと VM がどのように通信するかを説明します。"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 3b51276fe074282339d30d075547160277bee53f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2017
---
# <a name="understanding-outbound-connections-in-azure"></a>Azure の送信用接続の詳細

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure の仮想マシン (VM) は、パブリック IP アドレス空間で Azure 外部のエンドポイントと通信できます。 VM がパブリック IP アドレス空間の宛先への送信フローを開始すると、Azure は VM のプライベート IP アドレスをパブリック IP アドレスにマッピングし、戻りトラフィックが VM に届くようにします。

Azure では、送信接続を実現するために 3 つの異なる方法が提供されます。 それぞれの方法には独自の機能と制約があります。 各方法をよく調べて、ニーズに合う方法を選択してください。

| シナリオ | メソッド | 注 |
| --- | --- | --- |
| スタンドアロン VM (ロード バランサーなし、インスタンス レベルのパブリック IP アドレスなし) |既定の SNAT |Azure は SNAT のパブリック IP アドレスを関連付けます。 |
| 負荷分散 VM (VM にインスタンス レベルのパブリック IP アドレスなし) |ロード バランサーを使用する SNAT |Azure は SNAT でロード バランサーのパブリック IP を使用します。 |
| インスタンス レベル パブリック IP アドレスを含む VM (ロード バランサーあり、またはなし) |SNAT は使用されません。 |Azure は VM に割り当てられたパブリック IP を使用します。 |

VM がパブリック IP アドレス空間で Azure の外部のエンドポイントと通信しないようにするには、ネットワーク セキュリティ グループ (NSG) を使用してアクセスをブロックできます。 NSG の使用方法については、「[パブリック接続の防止](#preventing-public-connectivity)」を参照してください。

## <a name="standalone-vm-with-no-instance-level-public-ip-address"></a>インスタンス レベルのパブリック IP アドレスなしのスタンドアロン VM

このシナリオでは、VM は Azure Load Balancer プールに含まれず、インスタンス レベルのパブリック IP (ILPIP) アドレスは割り当てられていません。 VM が送信フローを作成すると、Azure が、送信フローのプライベート ソース IP アドレスをパブリック ソース IP アドレスに変換します。 この送信フローで使用されるパブリック IP アドレスは構成不可能であり、サブスクリプションのパブリック IP リソースの制限に対してカウントされません。 Azure は Source Network Address Translation (SNAT) を使用してこの機能を実行します。 パブリック IP アドレスの一時ポートを使用して、VM から送信される個々のフローが区別されます。 フローが作成されると、SNAT は一時ポートを動的に割り当てます。 ここでは、SNAT で使用される一時ポートを SNAT ポートと呼びます。

SNAT ポートは有限のリソースであり、不足する可能性があります。 使用方法を理解することが大切です。 1 つの SNAT ポートは、1 つの宛先 IP アドレスへの 1 つのフローで消費されます。 同じ宛先 IP アドレスへの複数のフローでは、各フローが 1 つの SNAT ポートを消費します。 これにより、同じパブリック IP アドレスから同じ宛先 IP アドレスに送られる複数のフローが一意であることが保証されます。 別の宛先 IP アドレスへの複数のフローは、宛先ごとに 1 つの SNAT ポートを共有します。 宛先 IP アドレスによってフローが一意になります。

[ロード バランサーの Log Analytics](load-balancer-monitor-log.md) と、[SNAT ポート不足メッセージを監視するためのアラート イベント ログ](load-balancer-monitor-log.md#alert-event-log)を使用して、送信接続の正常性を監視できます。 SNAT ポート リソースがなくなると、既存のフローによって SNAT ポートが解放されない限り、送信フローは失敗します。 ロード バランサーは、SNAT ポートを再利用するために 4 分間のアイドル タイムアウトを使用します。  以降の「[インスタンス レベル パブリック IP アドレスを含む VM (ロード バランサーあり、またはなし)](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer)」セクションと「[SNAT 不足の管理](#snatexhaust)」を参照してください。

## <a name="load-balanced-vm-with-no-instance-level-public-ip-address"></a>インスタンス レベルのパブリック IP アドレスなしの負荷分散 VM

このシナリオでは、VM は Azure Load Balancer プールに含まれます。  VM にパブリック IP アドレスは割り当てられていません。 パブリック IP フロントエンドとバックエンド プールをリンクするには、ロード バランサー リソースをルールで構成する必要があります。  この構成を完了しないと、「[インスタンス レベルのパブリック IP アドレスなしのスタンドアロン VM](load-balancer-outbound-connections.md#standalone-vm-with-no-instance-level-public-ip-address)」セクションのような動作になります。

負荷分散 VM が送信フローを作成すると、Azure が、送信フローのプライベート ソース IP アドレスをパブリック ロード バランサー フロントエンドのパブリック IP アドレスに変換します。 Azure は Source Network Address Translation (SNAT) を使用してこの機能を実行します。 ロード バランサーのパブリック IP アドレスの一時ポートを使用して、VM から送信される個々のフローが区別されます。 送信フローが作成されると、SNAT は一時ポートを動的に割り当てます。 ここでは、SNAT で使用される一時ポートを SNAT ポートと呼びます。

SNAT ポートは有限のリソースであり、不足する可能性があります。 使用方法を理解することが大切です。 1 つの SNAT ポートは、1 つの宛先 IP アドレスへの 1 つのフローで消費されます。 同じ宛先 IP アドレスへの複数のフローでは、各フローが 1 つの SNAT ポートを消費します。 これにより、同じパブリック IP アドレスから同じ宛先 IP アドレスに送られる複数のフローが一意であることが保証されます。 別の宛先 IP アドレスへの複数のフローは、宛先ごとに 1 つの SNAT ポートを共有します。 宛先 IP アドレスによってフローが一意になります。

[ロード バランサーの Log Analytics](load-balancer-monitor-log.md) と、[SNAT ポート不足メッセージを監視するためのアラート イベント ログ](load-balancer-monitor-log.md#alert-event-log)を使用して、送信接続の正常性を監視できます。 SNAT ポート リソースがなくなると、既存のフローによって SNAT ポートが解放されない限り、送信フローは失敗します。 ロード バランサーは、SNAT ポートを再利用するために 4 分間のアイドル タイムアウトを使用します。  次のセクションおよび「[SNAT 不足の管理](#snatexhaust)」を参照してください。

## <a name="vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer"></a>インスタンス レベル パブリック IP アドレスを含む VM (ロード バランサーあり、またはなし)

このシナリオでは、VM にはインスタンス レベルのパブリック IP (ILPIP) が割り当てられています。 VM が負荷分散されているかどうかは関係ありません。 ILPIP が使用されるとき、Source Network Address Translation (SNAT) は使用されません。 VM はすべての送信フローで ILPIP を使用します。 アプリケーションが多数の送信フローを開始したために SNAT 不足が発生した場合は、SNAT の制約を緩和するために ILPIP を割り当てることを検討してください。

## <a name="discovering-the-public-ip-used-by-a-given-vm"></a>所定の VM で使用されるパブリック IP の検出

送信接続のパブリック ソース IP アドレスを判別する方法は多数あります。 OpenDNS によって提供されるサービスで、VM のパブリック IP アドレスを表示できます。 nslookup コマンドを使用して、名前 myip.opendns.com に関する DNS クエリを OpenDNS Resolver に送信できます。 このサービスは、クエリの送信に使用されたソース IP アドレスを返します。 VM から次のクエリを実行すると、その VM で使用されるパブリック IP が応答として返されます。

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-public-connectivity"></a>パブリック接続の防止

VM による送信フローの作成が望ましくない状況や、送信フローで接続できる宛先、受信フローを開始できる宛先を管理する要件がある場合があります。 このような場合に、[ネットワーク セキュリティ グループ (NSG)](../virtual-network/virtual-networks-nsg.md) を使用して、VM が接続できる宛先や受信フローを開始できるパブリックの宛先を管理できます。 負荷分散された VM に NSG を適用するときは、[既定のタグ](../virtual-network/virtual-networks-nsg.md#default-tags)と[既定のルール](../virtual-network/virtual-networks-nsg.md#default-rules)に注意する必要があります。

VM が Azure Load Balancer からのヘルス プローブ要求を受け取ることができるようにしておいてください。 NSG が AZURE_LOADBALANCER 既定タグからのヘルス プローブ要求をブロックすると、VM のヘルス プローブが失敗して VM が停止しているとマークされます。 ロード バランサーはその VM への新しいフローの送信を停止します。

## <a name="snatexhaust"></a>SNAT 不足の管理

SNAT に使用されるエフェメラル ポートは、「[インスタンス レベルのパブリック IP アドレスなしのスタンドアロン VM](#standalone-vm-with-no-instance-level-public-ip-address)」および「[インスタンス レベルのパブリック IP アドレスなしの負荷分散 VM](#standalone-vm-with-no-instance-level-public-ip-address)」で説明されているように有限のリソースです。  

一般に、同じ宛先に対して多数の送信接続が開始されることが事前にわかっている場合や、送信接続エラーが観察される場合、または自分のせいで SNAT ポートが不足しているとサポートから指摘された場合、問題の軽減策としていくつかの選択肢があります。  それらをよく吟味して、実際のシナリオに最適な選択肢を判断してください。  それらを 1 つまたは複数組み合わせることが状況改善に役立つ場合もあります。

### <a name="assign-an-instance-level-public-ip-to-each-vm"></a>インスタンスレベル パブリック IP を個々の VM に割り当てる
これは、[インスタンスレベル パブリック IP を VM に割り当てる](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer)シナリオへの移行を伴う方法です。  パブリック IP のエフェメラル ポートが、それぞれのバックエンド プールに関連付けられている全 VM 間で共有されるシナリオとは対照的に、それぞれの VM に使用されるパブリック IP のエフェメラル ポートをその VM が独占できます。

### <a name="modify-application-to-use-connection-pooling"></a>接続プーリングを使用するようにアプリケーションを変更する
SNAT に使用されるエフェメラル ポートの需要は、アプリケーションに接続プーリングを使用することで減らすことができます。  同じ宛先に対するフローが連続すると、次々にポートが消費されます。  複数の要求に対して同じフローを再利用すれば、複数の要求で消費されるポートが 1 つで済みます。

### <a name="modify-application-to-use-less-aggressive-retry-logic"></a>再試行の頻度を抑えるようにアプリケーションのロジックを変更する
エフェメラル ポートの需要は、再試行の頻度を抑えたロジックを使用することで減らすことができます。  SNAT に使用されるエフェメラル ポートが不足しているときに、減退やバックオフ ロジックを使わず単純に再試行を繰り返す方法は、ポート不足の慢性化を引き起こします。  エフェメラル ポートには、4 分間のアイドル タイムアウト (調整不可) が設けられており、再試行の頻度が多すぎた場合、ポート不足は自然には解消されません。

## <a name="limitations"></a>制限事項

[複数の (パブリック) IP アドレスがロード バランサーに関連付けられている](load-balancer-multivip-overview.md)場合、すべてのパブリック IP アドレスが送信フローの候補になります。

Azure に使用されているアルゴリズムは、利用できる SNAT ポートの数をプールのサイズから判断します。  現時点では、これを構成によって変更することはできません。

送信接続には、4 分間のアイドル タイムアウトが設けられています。  この時間は調整できません。

利用できる SNAT ポートの数が、そのまま接続数に変換されるわけではないことに注意してください。 SNAT ポートがどのタイミングでいくつ割り当てられるかについて、またこの有限なリソースを管理する方法については、上記を参照してください。
