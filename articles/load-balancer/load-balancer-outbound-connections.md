---
title: "Azure の送信用接続の詳細 | Microsoft Docs"
description: "この記事では、Azure によって、パブリック インターネット サービスと VM がどのように通信するかを説明します。"
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: b8e225ba4374c73dbabac3dddab9ba37fa798a5a
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2018
---
# <a name="understanding-outbound-connections-in-azure"></a>Azure の送信用接続の詳細

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure の仮想マシン (VM) は、パブリック IP アドレス空間で Azure 外部のエンドポイントと通信できます。 VM がパブリック IP アドレス空間の宛先への送信フローを開始すると、Azure は VM のプライベート IP アドレスをパブリック IP アドレスにマッピングし、戻りトラフィックが VM に届くようにします。

Azure では、送信接続を実現するために 3 つの異なる方法が提供されます。 それぞれの方法には独自の機能と制約があります。 各方法をよく調べて、ニーズに合う方法を選択してください。

| シナリオ | 方法 | 注 |
| --- | --- | --- |
| スタンドアロン VM (ロード バランサーなし、インスタンス レベルのパブリック IP アドレスなし) |既定の SNAT |Azure は SNAT のパブリック IP アドレスを関連付けます。 |
| 負荷分散 VM (VM にインスタンス レベルのパブリック IP アドレスなし) |ロード バランサーを使用する SNAT |Azure は SNAT でロード バランサーのパブリック IP を使用します。 |
| インスタンス レベル パブリック IP アドレスを含む VM (ロード バランサーあり、またはなし) |SNAT は使用されません。 |Azure は VM に割り当てられたパブリック IP を使用します。 |

VM がパブリック IP アドレス空間で Azure の外部のエンドポイントと通信しないようにするには、ネットワーク セキュリティ グループ (NSG) を使用してアクセスをブロックできます。 NSG の使用方法については、「[パブリック接続の防止](#preventing-public-connectivity)」を参照してください。

## <a name="standalone-vm-with-no-instance-level-public-ip-address"></a>インスタンス レベルのパブリック IP アドレスなしのスタンドアロン VM

このシナリオでは、VM は Azure Load Balancer プールに含まれず、インスタンス レベルのパブリック IP (ILPIP) アドレスは割り当てられていません。 VM が送信フローを作成すると、Azure が、送信フローのプライベート ソース IP アドレスをパブリック ソース IP アドレスに変換します。 この送信フローで使用されるパブリック IP アドレスは構成不可能であり、サブスクリプションのパブリック IP リソースの制限に対してカウントされません。 Azure は Source Network Address Translation (SNAT) を使用してこの機能を実行します。 パブリック IP アドレスの一時ポートを使用して、VM から送信される個々のフローが区別されます。 フローが作成されると、SNAT は一時ポートを動的に割り当てます。 ここでは、SNAT で使用される一時ポートを SNAT ポートと呼びます。

SNAT ポートは有限のリソースであり、不足する可能性があります。 使用方法を理解することが大切です。 1 つの SNAT ポートは、1 つの宛先 IP への 1 つのフローで消費されます。 同じ宛先 IP アドレスとポートへの複数のフローでは、各フローが 1 つの SNAT ポートを消費します。 これにより、同じパブリック IP アドレスから同じ宛先 IP アドレスとポートに送られる複数のフローが一意であることが保証されます。 別の宛先 IP アドレスとポートへの複数のフローは、宛先ごとに 1 つの SNAT ポートを共有します。 宛先 IP アドレスとポートによってフローが一意になります。

[ロード バランサーの Log Analytics](load-balancer-monitor-log.md) と、[SNAT ポート不足メッセージを監視するためのアラート イベント ログ](load-balancer-monitor-log.md#alert-event-log)を使用して、送信接続の正常性を監視できます。 SNAT ポート リソースがなくなると、既存のフローによって SNAT ポートが解放されない限り、送信フローは失敗します。 ロード バランサーは、SNAT ポートを再利用するために 4 分間のアイドル タイムアウトを使用します。  以降の「[インスタンス レベル パブリック IP アドレスを含む VM (ロード バランサーあり、またはなし)](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer)」セクションと「[SNAT 不足の管理](#snatexhaust)」を参照してください。

## <a name="load-balanced-vm-with-no-instance-level-public-ip-address"></a>インスタンス レベルのパブリック IP アドレスなしの負荷分散 VM

このシナリオでは、VM は Azure Load Balancer プールに含まれます。  VM にパブリック IP アドレスは割り当てられていません。 パブリック IP フロントエンドとバックエンド プール間にリンクを作成するには、ロード バランサー リソースをロード バランサー規則で構成する必要があります。 この構成を完了しないと、前述の「[インスタンス レベルのパブリック IP アドレスなしのスタンドアロン VM](load-balancer-outbound-connections.md#standalone-vm-with-no-instance-level-public-ip-address)」セクションのような動作になります。

負荷分散 VM が送信フローを作成すると、Azure が、送信フローのプライベート ソース IP アドレスをパブリック ロード バランサー フロントエンドのパブリック IP アドレスに変換します。 Azure は Source Network Address Translation (SNAT) を使用してこの機能を実行します。 ロード バランサーのパブリック IP アドレスの一時ポートを使用して、VM から送信される個々のフローが区別されます。 送信フローが作成されると、SNAT は一時ポートを動的に割り当てます。 ここでは、SNAT で使用される一時ポートを SNAT ポートと呼びます。

SNAT ポートは有限のリソースであり、不足する可能性があります。 使用方法を理解することが大切です。 1 つの SNAT ポートは、1 つの宛先 IP アドレスとポートへの 1 つのフローで消費されます。 同じ宛先 IP アドレスとポートへの複数のフローでは、各フローが 1 つの SNAT ポートを消費します。 これにより、同じパブリック IP アドレスから同じ宛先 IP アドレスとポートに送られる複数のフローが一意であることが保証されます。 別の宛先 IP アドレスとポートへの複数のフローは、宛先ごとに 1 つの SNAT ポートを共有します。 宛先 IP アドレスとポートによってフローが一意になります。

[ロード バランサーの Log Analytics](load-balancer-monitor-log.md) と、[SNAT ポート不足メッセージを監視するためのアラート イベント ログ](load-balancer-monitor-log.md#alert-event-log)を使用して、送信接続の正常性を監視できます。 SNAT ポート リソースがなくなると、既存のフローによって SNAT ポートが解放されない限り、送信フローは失敗します。 ロード バランサーは、SNAT ポートを再利用するために 4 分間のアイドル タイムアウトを使用します。  次のセクションと「[SNAT 不足の管理](#snatexhaust)」を参照してください。

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

同じ宛先 IP アドレスとポートに対して多数の TCP 送信接続または UDP 送信接続が開始されることがわかっている場合、送信接続エラーが観察される場合、または SNAT ポートが不足しているとサポートから指摘された場合、一般的な軽減策の選択肢がいくつかあります。  それらをよく吟味して、実際のシナリオに最適な選択肢を判断してください。  それらを 1 つまたは複数組み合わせることが状況改善に役立つ場合もあります。

### <a name="modify-application-to-reuse-connections"></a>接続を再利用するようにアプリケーションを変更する 
SNAT に使用される一時ポートの需要は、アプリケーションで接続を再利用することで減らすことができます。  これには、既定で接続が再利用される HTTP/1.1 などのプロトコルが特に有効です。  また、HTTP が転送に使用されるその他のプロトコル (つまり REST) も活用できます。  要求ごとにアトミックな TCP 接続を個別に行うよりも、再利用を行う方が常に効率的です。再利用は、TCP トランザクションのパフォーマンス向上と著しい効率化につながります。

### <a name="modify-application-to-use-connection-pooling"></a>接続プーリングを使用するようにアプリケーションを変更する
アプリケーションでは接続プーリング スキームを利用できます。この場合、接続の固定セットに対して要求が内部的に分散されます (各要求で利用可能な接続が再利用されます)。  これにより、使用される一時ポートの数が制限され、環境の予測可能性が高まります。  さらにこれを使用すると、ある操作の応答で 1 つの接続がブロックされている際に同時に複数の操作を行えるようにして、要求のスループットを向上させることもできます。  接続プーリングは既に、アプリケーションの開発に使用しているフレームワーク、またはアプリケーションの構成設定の中に存在している可能性があります。  これは、接続の再利用と組み合わせることができます。同じ宛先 IP アドレスとポートに対する複数の要求で、予測可能な固定数のポートを消費します。同時に TCP トランザクションを非常に効率的に使用して、待ち時間とリソース使用率を抑えることができます。

### <a name="modify-application-to-use-less-aggressive-retry-logic"></a>再試行の頻度を抑えるようにアプリケーションのロジックを変更する
SNAT に使用される一時ポートが不足している場合、またはアプリケーションのエラーが発生している場合、減退やバックオフ ロジックを使わず単純に再試行を繰り返すと、ポート不足が発生したり持続したりします。 エフェメラル ポートの需要は、再試行の頻度を抑えたロジックを使用することで減らすことができます。   エフェメラル ポートには、4 分間のアイドル タイムアウト (調整不可) が設けられており、再試行の頻度が多すぎた場合、ポート不足は自然には解消されません。  そのため、アプリケーションがトランザクションを再試行する方法と頻度を考慮することは、設計において非常に重要です。

### <a name="assign-an-instance-level-public-ip-to-each-vm"></a>インスタンスレベル パブリック IP を個々の VM に割り当てる
これは、[インスタンスレベル パブリック IP を VM に割り当てる](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer)シナリオへの移行を伴う方法です。  パブリック IP のエフェメラル ポートが、それぞれのバックエンド プールに関連付けられている全 VM 間で共有されるシナリオとは対照的に、それぞれの VM に使用されるパブリック IP のエフェメラル ポートをその VM が独占できます。  IP アドレスの追加コストや、大量の IP アドレスを個別にホワイトリスト登録することの潜在的な影響など、考慮すべきトレードオフがあります。

## <a name="limitations"></a>制限事項

[複数の (パブリック) IP アドレスがロード バランサーに関連付けられている](load-balancer-multivip-overview.md)場合、すべてのパブリック IP アドレスが送信フローの候補になります。

Azure に使用されているアルゴリズムは、利用できる SNAT ポートの数をプールのサイズから判断します。  現時点では、これを構成によって変更することはできません。

送信接続には、4 分間のアイドル タイムアウトが設けられています。  この時間は調整できません。

利用できる SNAT ポートの数が、そのまま接続数に変換されるわけではないことに注意してください。 SNAT ポートがどのタイミングでいくつ割り当てられるかについて、また[この有限なリソースを管理する方法](#snatexhaust)については、前のセクションを参照してください。
