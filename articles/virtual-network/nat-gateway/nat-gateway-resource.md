---
title: NAT ゲートウェイ リソースを使用した仮想ネットワークの設計
titleSuffix: Azure Virtual Network NAT
description: NAT ゲートウェイ リソースを使用して仮想ネットワークを設計する方法について説明します。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2021
ms.author: allensu
ms.openlocfilehash: d0af0d38ef53e4ba90dcf0e7f4cbcc60c52b87a8
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373139"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>NAT ゲートウェイ リソースを使用した仮想ネットワークの設計

NAT ゲートウェイ リソースは、[Virtual Network NAT](nat-overview.md) の構成要素であり、仮想ネットワークの 1 つまたは複数のサブネットに対してアウトバウンド インターネット接続を提供します。 どの NAT ゲートウェイを使用するかは、仮想ネットワークのサブネットで指定します。 NAT は、サブネットの送信元ネットワーク アドレス変換 (SNAT) を行う役割を果たします。  仮想マシンがアウトバウンド フローを作成するときに使用される静的 IP アドレスは、NAT ゲートウェイ リソースによって指定されます。 静的 IP アドレスは、パブリック IP アドレス リソース (PIP) かパブリック IP プレフィックス リソース、またはその両方から得られます。 パブリック IP プレフィックス リソースが使用されている場合は、パブリック IP プレフィックス リソース全体のすべての IP アドレスが NAT ゲートウェイ リソースによって使用されます。 NAT ゲートウェイ リソースは、どちらの側からも合計で最大 16 個の静的 IP アドレスを使用できます。

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="インターネットへのアウトバウンド接続のための Virtual Network NAT">
</p>

*図:インターネットへのアウトバウンド接続のための Virtual Network NAT*

## <a name="how-to-deploy-nat"></a>NAT をデプロイする方法

NAT ゲートウェイは簡単に構成、使用できるように意図されています。  

NAT ゲートウェイ リソース:
- リージョンまたはゾーンの NAT ゲートウェイ リソースを作成する
- パブリック IP アドレスを割り当てる。
- 必要に応じて、TCP アイドル タイムアウトを変更する (省略可)。  既定値を変更する<ins>前に</ins>、[タイマー](#timers)を確認してください。

Virtual Network:
- NAT ゲートウェイを使用するように仮想ネットワーク サブネットを構成する。

ユーザー定義ルートは必要ありません。


## <a name="design-guidance"></a>設計ガイダンス

NAT を使用した仮想ネットワークの設計に関する考慮事項をよく理解するために、次のセクションを振り返ってみましょう。  

### <a name="connecting-to-azure-services"></a>Azure サービスへの接続

Azure サービスに接続するときは、[プライベート リンク](../../private-link/private-link-overview.md)を活用することをお勧めします。 

プライベート リンクによってご利用の仮想ネットワークに Azure サービス リソースが関連付けられ、Azure サービス リソースへのアクセスが制御されます。 たとえば、Azure Storage にアクセスするとき、接続が完全にプライベートになるよう、ストレージのプライベート エンドポイントを使用します。

### <a name="connecting-to-the-internet"></a>インターネットへの接続

パブリック エンドポイントに接続する必要があるあらゆる運用ワークロードの送信シナリオには NAT が推奨されます。 次のシナリオは、送信の NAT ゲートウェイを受信と共存させる方法の例です。

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>インスタンスレベル パブリック IP を使用した VM と NAT

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP." width="300" title="インスタンスレベル パブリック IP を使用した VM と Virtual Network NAT">
</p>

*図:インスタンスレベル パブリック IP を使用した VM と Virtual Network NAT*

| Direction | リソース |
|:---:|:---:|
| 受信 | インスタンスレベル パブリック IP を使用した VM |
| 送信 | NAT Gateway |

VM からのアウトバウンドには NAT ゲートウェイが使用されます。  外部からのインバウンドは影響を受けません。

#### <a name="nat-and-vm-with-standard-public-load-balancer"></a>Standard パブリック Load Balancer を使用した VM と NAT

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with a public load balancer." width="350" title="パブリック Load Balancer を使用した VM と Virtual Network NAT">
</p>

*図:パブリック Load Balancer を使用した VM と Virtual Network NAT*

| Direction | リソース |
|:---:|:---:|
| 受信 | パブリック ロード バランサー |
| 送信 | NAT Gateway |

負荷分散規則またはアウトバウンド規則に含まれるアウトバウンド構成は、NAT ゲートウェイに取って代わられます。  外部からのインバウンドは影響を受けません。

#### <a name="nat-and-vm-with-instance-level-public-ip-and-standard-public-load-balancer"></a>インスタンスレベル パブリック IP と Standard パブリック Load Balancer を使用した VM と NAT

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="425" title="インスタンスレベル パブリック IP とパブリック Load Balancer を使用した VM と Virtual Network NAT">
</p>

*図:インスタンスレベル パブリック IP とパブリック Load Balancer を使用した VM と Virtual Network NAT*

| Direction | リソース |
|:---:|:---:|
| 受信 | インスタンスレベル パブリック IP とパブリック Load Balancer を使用した VM |
| 送信 | NAT Gateway |

負荷分散規則またはアウトバウンド規則に含まれるアウトバウンド構成は、NAT ゲートウェイに取って代わられます。  この場合も、VM からのアウトバウンドには NAT ゲートウェイが使用されます。  外部からのインバウンドは影響を受けません。

## <a name="performance"></a>パフォーマンス

各 NAT ゲートウェイ リソースは、最大 50 Gbps のスループットを提供できます。 デプロイを複数のサブネットに分割し、各サブネットまたはサブネットのグループを NAT ゲートウェイに割り当てて、スケールアウトすることができます。

各 NAT ゲートウェイによって、割り当てられたアウトバウンド IP アドレスごとに、TCP および UDP に対して 64,000 フローがサポートされます。  詳細については、送信元ネットワーク アドレス変換 (SNAT) に関する次のセクションを参照してください。また、特定の問題解決のガイダンスについては、[トラブルシューティングに関する記事](./troubleshoot-nat.md)を参照してください。

## <a name="source-network-address-translation"></a>送信元ネットワーク アドレス変換

送信元ネットワーク アドレス変換 (SNAT) は、別の IP アドレスが送信元となるようにフローの送信元を書き換えるものです。  NAT ゲートウェイ リソースでは、SNAT のバリアントが使用されます。これは一般にポート アドレス変換 (PAT) と呼ばれます。 PAT では、送信元アドレスと送信元ポートが書き換えられます。 SNAT では、プライベート アドレスの数とその変換後のパブリック アドレスの数との間に固定的な関係はありません。  

### <a name="fundamentals"></a>基礎

基本的な概念を説明するために、4 つのフローの例を見ていきましょう。  NAT ゲートウェイはパブリック IP アドレス リソース 65.52.1.1 を使用しており、VM が 65.52.0.1 に接続しようとしています。

| Flow | 送信元のタプル | 送信先のタプル |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |

PAT の実行後、これらのフローは次のようになります。

| Flow | 送信元のタプル | 送信元のタプル (SNAT 変換後) | 送信先のタプル | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | **65.52.1.1:1234** | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | **65.52.1.1:1235** | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | **65.52.1.1:1236** | 65.52.0.1:80 |

送信先から見ると、フローの送信元は 65.52.0.1 (SNAT 変換後の送信元のタプル) で、記載したポートが割り当てられています。  前出の表に示した PAT は、ポート マスカレード SNAT とも呼ばれます。  IP とポートの背後には、複数の送信元プライベート アドレスがマスカレードされています。  

#### <a name="source-snat-port-reuse"></a>送信元 (SNAT) ポートの再利用

NAT ゲートウェイにより、状況に応じて送信元 (SNAT) ポートが再利用されます。  この概念を前に示したフローのセットに対する追加フローとして、以下に示します。  この例の VM は、65.52.0.2 へのフローです。

| Flow | 送信元のタプル | 送信先のタプル |
|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

NAT ゲートウェイによって、フロー 4 が他の送信先にも使用できるポートに変換される可能性が高いです。  IP アドレスのプロビジョニングの適切なサイズ設定の詳細については、「[スケーリング](#scaling)」を参照してください。

| Flow | 送信元のタプル | 送信元のタプル (SNAT 変換後) | 送信先のタプル | 
|:---:|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.1.1:**1234** | 65.52.0.2:80 |

上述の例で送信元ポートが割り当てられた特定の方式をすべてに当てはめるのは避けてください。  前出の表は、あくまで基本的な概念を例示したものです。

NAT による SNAT は、いくつかの点で [Load Balancer](../../load-balancer/load-balancer-outbound-connections.md) とは異なります。

### <a name="on-demand"></a>オンデマンド

NAT では、新しいアウトバウンド トラフィック フローにオンデマンドで SNAT ポートが割り当てられます。 インベントリにあるすべての空き SNAT ポートは、NAT が構成されているサブネット上のすべての仮想マシンによって使用されます。 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T." width="550" title="Virtual Network NAT のオンデマンド アウトバウンド SNAT">
</p>

*図:Virtual Network NAT のオンデマンド アウトバウンド SNAT*

アウトバウンド フローは、任意の IP 構成の仮想マシンがオンデマンドで作成できます。  最悪のケースに備えたインスタンスごとのオーバープロビジョニングも含め、インスタンスごとの事前割り当て計画は必要ありません。  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T with exhaustion threshold." width="550" title="枯渇状況での相違点">
</p>

*図:枯渇状況での相違点*

SNAT ポートが解放されると、NAT が構成されているサブネット上の任意の仮想マシンがそのポートを使用できるようになります。  割り当てはオンデマンドで行われるので、サブネット上の動的かつ多様なワークロードが必要に応じて SNAT ポートを使用できます。  空き SNAT ポートがインベントリにある限り、SNAT フローは正常に動作します。 インベントリが大きいことの効果は、SNAT ポートのホット スポットで顕著になります。 SNAT ポートをさほど必要としていない仮想マシンのために、それらのポートが未使用のまま放置されることはありません。

### <a name="scaling"></a>Scaling

NAT のスケーリングは主に、共有された空き SNAT ポート インベントリを管理する機能です。 NAT ゲートウェイ リソースにアタッチされたすべてのサブネットのピーク アウトバウンド フローを見越して、NAT には十分な SNAT ポート インベントリを確保する必要があります。  SNAT ポート インベントリは、パブリック IP アドレス リソース、パブリック IP プレフィックス リソース、またはその両方を使用して作成できます。  

>[!NOTE]
>パブリック IP プレフィックス リソースを割り当てる場合は、パブリック IP プレフィックス全体が使用されます。  パブリック IP プレフィックス リソースを割り当ててから、個々の IP アドレスを分割して他のリソースに割り当てることはできません。  パブリック IP プレフィックスの個々の IP アドレスを複数のリソースに割り当てる場合は、パブリック IP プレフィックス リソースから個々のパブリック IP アドレスを作成し、パブリック IP プレフィックス リソース自体ではなくそれらを、必要に応じて割り当てる必要があります。

SNAT は、プライベート アドレスを 1 つまたは複数のパブリック IP アドレスにマップするものです。そのプロセスの中で送信元アドレスと送信元ポートは書き換えられます。 この変換を行うために、NAT ゲートウェイ リソースは、構成されているパブリック IP アドレス 1 つにつき、64,000 個のポート (SNAT ポート) を使用します。 NAT ゲートウェイ リソースは、16 個の IP アドレスおよび ‭1,024,000‬ 個の SNAT ポートにまでスケールアップできます。 パブリック IP プレフィックス リソースが用意されている場合、プレフィックス内の各 IP アドレスによって SNAT ポート インベントリが提供されます。 さらにパブリック IP アドレスを追加すると、インベントリの空き SNAT ポートが増えます。 TCP と UDP は、別個の SNAT ポート インベントリであり、互いに独立しています。

NAT ゲートウェイ リソースにより、状況に応じて送信元 (SNAT) ポートが再利用されます。 スケーリングを目的とする設計ガイダンスとして、フローごとに新しい SNAT ポートが必要になることを前提に、アウトバウンド トラフィックに使用可能な IP アドレスの総数をスケーリングする必要があります。  設計するスケールを慎重に検討し、それに応じて IP アドレスの数をプロビジョニングする必要があります。

異なる送信先への SNAT ポートは、可能な場合、再利用される可能性が最も高いです。 そして、SNAT ポートが枯渇状況に近づくと、フローが成功しないことがあります。  

例については、[SNAT の基礎](#source-network-address-translation)に関する記事を参照してください。


### <a name="protocols"></a>プロトコル

NAT ゲートウェイ リソースは、UDP フローと TCP フローの IP ヘッダーおよび IP トランスポート ヘッダーに作用します。アプリケーション レイヤーのペイロードには依存しません。  他の IP プロトコルはサポートされません。

### <a name="timers"></a>タイマー

>[!IMPORTANT]
>アイドル タイマーを長くすると、SNAT が枯渇する可能性をむやみに高めることにつながります。 指定するタイマーが長いほど、SNAT ポートは長時間 NAT によって保持され続け、最終的にアイドル タイムアウトに達するまでは解放されません。 アイドル タイムアウトしたフローは、その後失敗に終わるので、SNAT ポート インベントリは無駄に消費されることになります。  2 時間で失敗するフローなら、既定値の 4 分でも失敗します。 アイドル タイムアウトの増加は最終手段であり、慎重に用いる必要があります。 フローが決してアイドル状態にならないのであれば、アイドル タイマーの影響は受けません。

TCP アイドル タイムアウトは、すべてのフローを対象に、4 分 (既定値) から 120 分 (2 時間) の範囲で調整できます。  さらに、フロー上のトラフィックに関して、アイドル タイマーをリセットすることができます。  長時間にわたるアイドル接続のリフレッシュやエンドポイントの生存確認に推奨されるパターンは、TCP キープアライブです。  TCP キープアライブは、エンドポイントからは重複する ACK として認識され、オーバーヘッドが低く、アプリケーション レイヤーからは見えません。

SNAT ポート解放には次のタイマーが使用されます。

| Timer | 値 |
|---|---|
| TCP FIN | 60 秒 |
| TCP RST | 10 秒 |
| TCP half open | 30 秒 |

SNAT ポートは、同じ送信先 IP アドレスおよび同じ送信先ポートに対して 5 秒後に再利用可能な状態となります。

>[!NOTE] 
>これらのタイマー設定は、変更されることがあります。 記載した値は、トラブルシューティングでの利用を意図したものです。特定のタイマーを前提とした判断は、現時点では避けてください。

## <a name="limitations"></a>制限事項

- Basic Load Balancer と Basic Public IP アドレスは NAT と互換性がありません。 代わりに、Standard SKU Load Balancer とパブリック IP を使用してください。
- NAT ゲートウェイ経由の IP フラグメンテーションはサポートされていません。

## <a name="next-steps"></a>次のステップ

* [仮想ネットワーク NAT](nat-overview.md) について学習する。
* [NAT ゲートウェイ リソースのメトリックとアラート](nat-metrics.md)について学習する。
* [NAT ゲートウェイ リソースのトラブルシューティング](troubleshoot-nat.md)について学習する。
