---
title: Azure の Outbound connections
titleSuffix: Azure Load Balancer
description: この記事では、Azure によって、パブリック インターネット サービスと VM がどのように通信するかを説明します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 0e46905ad280cd76d66befb1156e428b23f35664
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235643"
---
# <a name="outbound-connections-in-azure"></a>Azure の Outbound connections

Azure Load Balancer では、いくつかの異なるメカニズムを通じて顧客デプロイのアウトバウンド接続を提供します。 この記事では、どのようなシナリオがあり、それらがいつ適用されるかについて説明するほか、それらのシナリオのしくみと管理方法について説明します。 Azure ロード バランサーを経由するアウトバウンド接続で問題が発生した場合は、[アウトバウンド接続のトラブルシューティング ガイド](../load-balancer/troubleshoot-outbound-connection.md)を参照してください。

>[!NOTE] 
>この記事は、Resource Manager デプロイメントのみを対象とします。 Azure のすべてのクラシック デプロイメント シナリオについては、「[送信接続 (クラシック)](load-balancer-outbound-connections-classic.md)」をご覧ください。

Azure のデプロイは、パブリック IP アドレス空間で Azure 外部のエンドポイントと通信できます。 インスタンスがパブリック IP アドレス空間の宛先への送信フローを開始すると、Azure によってプライベート IP アドレスがパブリック IP アドレスに動的にマッピングされます。 このマッピングが作成されると、この送信フローの戻りトラフィックも、フローの送信元であるプライベート IP アドレスに到達できます。

Azure は送信元ネットワーク アドレス変換 (SNAT) を使用してこの機能を実行します。 複数のプライベート IP アドレスが単一のパブリック IP アドレスでマスカレードされている場合、Azure では[ポート アドレス変換 (PAT)](#pat) を使用してプライベート IP アドレスがマスカレードされます。 エフェメラル ポートが PAT に使用され、プール サイズに基づいて[事前に割り当て](#preallocatedports)られます。

[送信シナリオ](#scenarios)は複数あります。 必要に応じて、これらのシナリオを組み合わせることができます。 これらを注意深く確認して、実際のデプロイメント モデルとアプリケーション シナリオに適用される際の機能、制約、パターンを把握してください。 また、[これらのシナリオを管理する](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)ためのガイダンスを確認してください。

>[!IMPORTANT] 
>Standard Load Balancer および Standard パブリック IP では、アウトバウンド接続に新しい機能とさまざまな動作が導入されています。  これらは Basic SKU と同じではありません。  Standard SKU を操作するときにアウトバウンド接続が必要な場合は、Standard パブリック IP アドレスまたは Standard パブリック Load Balancer で明示的に定義する必要があります。  これには、内部 Standard Load Balancer を使用する場合のアウトバウンド接続の作成が含まれます。  Standard パブリック Load Balancer では常にアウトバウンド規則を使用することをお勧めします。  [シナリオ 3](#defaultsnat) は、Standard SKU では利用できません。  つまり、内部 Standard Load Balancer が使用されているときに、アウトバウンド接続が必要な場合、バックエンド プール内の VM に対してアウトバウンド接続を作成する手順を行う必要があります。  アウトバウンド接続のコンテキストでは、単一スタンドアロン VM、可用性セット内のすべての VM、VMSS のすべてのインスタンスがグループとして動作します。 つまり、可用性セット内の単一 VM が Standard SKU に関連付けられている場合、この可用性セット内のすべての VM インスタンスが、Standard SKU に関連付けられている場合と同じ規則に従って動作するようになります。個々のインスタンスが直接関連付けられていない場合でも同様です。 この動作は、ロード バランサーに複数のネットワーク インターフェイスカードが接続されているスタンドアロン VM の場合にも見られます。 1 つの NIC をスタンドアロンとして追加された場合、同じ動作が行われます。 このドキュメント全体をよく読み、全体的な概念を理解し、SKU 間の違いについて [Standard Load Balancer](load-balancer-standard-overview.md) を確認し、[アウトバウンド規則](load-balancer-outbound-rules-overview.md)を確認してください。  アウトバウンド規則を使用することで、アウトバウンド接続のすべての側面を細かく制御できます。

## <a name="scenario-overview"></a><a name="scenarios"></a>シナリオの概要

[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) を使用している場合、Azure Load Balancer と関連するリソースは明示的に定義されます。  現在 Azure には、Azure Resource Manager リソースの送信接続を実現するのに 3 つの異なる方法があります。 

| SKU | シナリオ | Method | IP プロトコル | 説明 |
| --- | --- | --- | --- | --- |
| Standard、Basic | [1.インスタンス レベル パブリック IP アドレスを含む VM (ロード バランサーあり、またはなし)](#ilpip) | SNAT (ポート マスカレードは不使用) | TCP、UDP、ICMP、ESP | インスタンスの NIC の IP 構成に割り当てられたパブリック IP が Azure によって使用されます。 インスタンスには、使用可能なすべてのエフェメラル ポートがあります。 Standard Load Balancer を使用しているときにパブリック IP が仮想マシンに割り当てられている場合、[アウトバウンド規則](load-balancer-outbound-rules-overview.md)はサポートされません。 |
| Standard、Basic | [2.VM に関連付けられたパブリック ロード バランサー (インスタンスにパブリック IP アドレスなし)](#lb) | ロード バランサー フロントエンドを使用したポート マスカレード (PAT) による SNAT | TCP、UDP |Azure によってパブリック ロード バランサー フロントエンドのパブリック IP アドレスが複数のプライベート IP アドレスと共有されます。 Azure では、フロントエンドのエフェメラル ポートが PAT に使用されます。 Standard Load Balancer を使用する場合は、[アウトバウンド規則](load-balancer-outbound-rules-overview.md)を使って、アウトバウンド接続を明示的に定義する必要があります。 |
| なし、または Basic | [3.スタンドアロン VM (ロード バランサーなし、パブリック IP アドレスなし)](#defaultsnat) | ポート マスカレード (PAT) による SNAT | TCP、UDP | Azure によって自動的に SNAT 用のパブリック IP アドレスが指定され、このパブリック IP アドレスが可用性セットの複数のプライベート IP アドレスと共有されてから、このパブリック IP アドレスのエフェメラル ポートが使用されます。 このシナリオは、前のシナリオのフォールバックです。 可視性と制御が必要は場合は推奨されません。 |

VM がパブリック IP アドレス空間で Azure の外部のエンドポイントと通信しないようにする場合、必要に応じてネットワーク セキュリティ グループ (NSG) を使用してアクセスをブロックできます。 NSG の使用の詳細については、「[送信接続の防止](#preventoutbound)」で説明します。 送信アクセスがない仮想ネットワークの設計、実装、および管理のガイダンスについては、この記事の範囲外です。

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>シナリオ 1:パブリック IP アドレスありの VM

このシナリオでは、VM にパブリック IP が割り当てられています。 送信接続に関する限り、VM が負荷分散されているかどうかは関係ありません。 このシナリオは他のシナリオよりも優先されます。 パブリック IP アドレスが使用されている場合、VM は、すべての送信フローにそのパブリック IP アドレスを使用します。  

VM に割り当てられたパブリック IP は (1 対多ではなく) 1 対 1 の関係であり、1 対 1 のステートレス NAT として実装されます。  ポート マスカレード (PAT) は使用されず、VM は使用可能なすべてのエフェメラル ポートを備えます。

アプリケーションが多数の送信フローを開始したために SNAT ポートの枯渇が発生している場合は、[SNAT の制約を緩和するためにパブリック IP アドレス](../load-balancer/troubleshoot-outbound-connection.md#assignilpip)を割り当てることを検討してください。 その全体像については、[SNAT 不足の管理](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)に関するセクションを確認してください。

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>シナリオ 2:パブリック IP アドレスなしの負荷分散 VM

このシナリオでは、VM はパブリック Load Balancer バックエンド プールに含まれます。 VM にパブリック IP アドレスは割り当てられていません。 パブリック IP フロントエンドとバックエンド プール間にリンクを作成するには、ロード バランサー リソースをロード バランサー規則で構成する必要があります。

この規則の構成を完了しないと、動作は、[パブリック IP なしのスタンドアロン VM](#defaultsnat) のシナリオで説明されたとおりになります。 成功するために正常性プローブのバックエンド プールの作業リスナーを規則に含める必要はありません。

負荷分散 VM が送信フローを作成すると、Azure が、送信フローのプライベート ソース IP アドレスをパブリック ロード バランサー フロントエンドのパブリック IP アドレスに変換します。 Azure は、SNAT を使用してこの機能を実行します。 また、Azure は、[PAT](#pat) を使用して、複数のプライベート IP アドレスをパブリック IP アドレスでマスカレードします。 

ロード バランサーのパブリック IP アドレス フロントエンドのエフェメラル ポートを使用して、VM から送信される個々のフローが区別されます。 送信フローが作成されると、[事前に割り当てられたエフェメラル ポート](#preallocatedports)が SNAT によって動的に使用されます。 ここでは、SNAT で使用されるエフェメラル ポートを SNAT ポートと呼びます。

SNAT ポートは、「[SNAT と PAT の理解](#snat)」の説明のとおり事前に割り当てられます。 これは有限のリソースであり、不足する可能性があります。 これらがどのように[消費される](#pat)のかを理解することが重要です。 この消費のための設計と移行を必要に応じて行う方法については、[SNAT 不足の管理](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)に関するセクションを確認してください。

[複数のパブリック IP アドレスが Load Balancer Basic に関連付けられている](load-balancer-multivip-overview.md)場合、それらのパブリック IP アドレスはどれもアウトバウンド フローの候補になり、その 1 つがランダムに選択されます。  

Load Balancer Basic でアウトバウンド接続の正常性を監視するために、[Load Balancer 用の Azure Monitor ログ](load-balancer-monitor-log.md)と、SNAT ポート不足メッセージを監視するための[アラート イベント ログ](load-balancer-monitor-log.md#alert-event-log)を使用できます。

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>シナリオ 3:パブリック IP アドレスなしのスタンドアロン VM

このシナリオでは、VM はパブリック ロード バランサー プールの一部ではなく (また内部の Standard Load Balancer プールの一部でもなく)、パブリック IP アドレスは割り当てられていません。 VM が送信フローを作成すると、Azure が、送信フローのプライベート ソース IP アドレスをパブリック ソース IP アドレスに変換します。 この送信フローで使用されるパブリック IP アドレスは構成不可能であり、サブスクリプションのパブリック IP リソースの制限に対してカウントされません。 このパブリック IP アドレスはユーザーのものではなく、予約することはできません。 VM、可用性セット、または仮想マシン スケール セットを再デプロイすると、このパブリック IP アドレスは解放され、新しいパブリック IP アドレスが要求されます。 IP アドレスをホワイトリストに登録する場合は、このシナリオを使用しないでください。 代わりに、送信シナリオと、送信接続で使用されるパブリック IP アドレスを明示的に宣言する他の 2 つのシナリオのいずれかを使用します。

>[!IMPORTANT] 
>このシナリオは、内部 Basic Load Balancer が接続されている場合に __のみ__ 適用されます。 内部 Standard Load Balancer が VM に接続されている場合、シナリオ 3 は __利用できません__。  内部 Standard Load Balancer を使用することに加え、[シナリオ 1](#ilpip) または[シナリオ 2](#lb) を明示的に作成する必要があります。

Azure は、SNAT とポート マスカレード ([PAT](#pat)) を使用してこの機能を実行します。 このシナリオは、使用される IP アドレスに対するコントロールがない点を除いて、[シナリオ 2](#lb) に類似しています。 これは、シナリオ 1 および 2 がない場合のフォールバック シナリオです。 このシナリオは、送信アドレスに対するコントロールが必要な場合は推奨されません。 送信接続がアプリケーションの重要な部分である場合は、別のシナリオを選ぶ必要があります。

SNAT ポートは、「[SNAT と PAT の理解](#snat)」の説明のとおり事前に割り当てられます。  可用性セットを共有している VM の数によって、適用される事前割り当ての階層が決まります。  事前割り当て (1024 SNAT ポート) を決定する目的の場合、可用性セットのないスタンドアロン VM は、事実上 1 のプールです。 SNAT ポートは有限のリソースであり、不足する可能性があります。 これらがどのように[消費される](#pat)のかを理解することが重要です。 この消費のための設計と移行を必要に応じて行う方法については、[SNAT 不足の管理](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)に関するセクションを確認してください。

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>組み合わされた複数のシナリオ

これまでのセクションで説明したシナリオを組み合わせることで、特定の結果を実現することができます。 複数のシナリオがある場合は、次の優先順位が適用されます。[シナリオ 1](#ilpip) は、[シナリオ 2](#lb) と[シナリオ 3](#defaultsnat) に優先します。 [シナリオ 2](#lb) は[シナリオ 3](#defaultsnat) をオーバーライドします。

たとえば、アプリケーションが限られた数の宛先への送信接続に大きく依存している一方で、ロード バランサー フロントエンド経由で受信フローも受け取っている Azure Resource Manager デプロイがあります。 この場合、シナリオ 1 と 2 を組み合わせて軽減を行えます。 その他のパターンについては、[SNAT 不足の管理](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)に関するセクションを確認してください。

### <a name="multiple-frontends-for-outbound-flows"></a><a name="multife"></a>送信フローの複数のフロントエンド

#### <a name="standard-load-balancer"></a>Standard Load Balancer

[複数の (パブリック) IP フロントエンド](load-balancer-multivip-overview.md)が存在する場合、Standard Load Balancer では、発信フローのすべての候補が同時に使用されます。 発信接続に対して負荷分散ルールが有効になっている場合、各フロントエンドは使用できる事前割当 SNAT ポートの数を増やします。

新しい負荷分散ルール オプションを使用して、発信接続にフロントエンド IP アドレスが使用されないようにすることができます。

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

通常、`disableOutboundSnat` オプションは既定で _false_ になり、このルールによって負荷分散ルールのバックエンド プール内の関連付けられた VM の送信 SNAT がプログラムされることを示します。 `disableOutboundSnat` を _true_ に変更すると、Load Balancer がこの負荷分散ルールのバックエンド プール内の VM の送信接続に、関連付けられたフロントエンド IP アドレスを使用しないようにすることができます。  また、「[組み合わされた複数のシナリオ](#combinations)」で説明されているとおり、送信フローに特定の IP アドレスを指定することもできます。

#### <a name="load-balancer-basic"></a>Load Balancer Basic

[複数の (パブリック) IP フロントエンド](load-balancer-multivip-overview.md)が送信フローの候補である場合、送信フローに使用される 1 つのフロントエンドが Load Balancer Basic によって選択されます。 この選択は構成することができません。選択アルゴリズムはランダムであると考える必要があります。 「[組み合わされた複数のシナリオ](#combinations)」で説明されているとおり、送信フローに特定の IP アドレスを指定できます。

### <a name="availability-zones"></a><a name="az"></a> 可用性ゾーン

[可用性ゾーンありの Standard Load Balancer](load-balancer-standard-availability-zones.md) を使用する場合、ゾーン冗長フロントエンドでゾーン冗長発信 SNAT 接続を提供できます。また、ゾーン エラーが発生しても SNAT プログラミングは継続されます。  ゾーンのフロントエンドを使用する場合、発信 SNAT 接続の有効期間は属するゾーンと同じです。

## <a name="understanding-snat-and-pat"></a><a name="snat"></a>SNAT と PAT の理解

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>ポート マスカレード SNAT (PAT)

パブリック ロード バランサー リソースが VM インスタンスに関連付けられている (専用のパブリック ID アドレスがない) 場合、各送信接続のソースは書き換えられます。 ソースは、仮想ネットワークのプライベート IP アドレス空間からロード バランサー フロントエンドのパブリック IP アドレスに書き換えられます。 パブリック IP アドレス空間では、フローの 5 タプル (ソース IP アドレス、ソース ポート、IP 転送プロトコル、宛先 IP アドレス、宛先ポート) は一意である必要があります。 ポート マスカレード SNAT は、TCP または UDP IP プロトコルで使用できます。

これを実現するために、プライベート ソース IP アドレスの書き換え後にエフェメラル ポート (SNAT ポート) が使用されます。これは複数のフローが単一のパブリック IP アドレスから送信されるためです。 SNAT アルゴリズムをマスカレードしているポートは、UDP 対 TCP とは異なる方法で SNAT ポートを割り当てます。

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>TCP SNAT ポート

1 つの SNAT ポートは、単一の宛先 IP アドレス、ポートへのフローごとに使用されます。 同じ宛先 IP アドレス、ポート、およびプロトコルへの複数の TCP フローでは、各 TCP フローが単一の SNAT ポートを使用します。 これにより、同じパブリック IP アドレスから送信されて同じ宛先 IP アドレス、ポート、プロトコルに移動する複数のフローが、一意であることが保証されます。 

別の宛先 IP アドレス、ポート、プロトコルへの複数のフローは、宛先ごとに 1 つの SNAT ポートを共有します。 宛先 IP アドレス、ポート、プロトコルによってフローは一意になります。パブリック IP アドレス空間でフローを区別するための追加のソース ポートは必要ありません。

#### <a name="udp-snat-ports"></a><a name="udp"></a> UDP SNAT ポート

UDP SNAT ポートは、TCP SNAT ポートではなく、別のアルゴリズムによって管理されます。  ロード バランサーは、UDP の "ポート制限された Cone NAT" と呼ばれるアルゴリズムを使用します。  1 つの SNAT ポートは、宛先 IP アドレス、ポートに関係なく、各フローに対して使用されます。

#### <a name="snat-port-reuse"></a>SNAT ポートの再利用

解放されたポートは必要に応じて再利用できます。  SNAT ポートは、特定のシナリオに対する最低から最高までのシーケンスと見なすことができます。最初に利用できる SNAT ポートは新しい接続に利用されます。 
 
#### <a name="exhaustion"></a>ポート不足

SNAT ポート リソースがなくなると、既存のフローによって SNAT ポートが解放されない限り、送信フローは失敗します。 Load Balancer はフローが終了すると SNAT ポートを回収します。また、[4 分間のアイドル タイムアウト](../load-balancer/troubleshoot-outbound-connection.md#idletimeout)を使用して、アイドル フローからの SNAT ポートを回収します。

一般的に UDP SNAT ポートは、使用されるアルゴリズムの違いが原因で、TCP SNAT ポートと比べてかなり迅速に使い果たされてしまいます。 この違いを念頭に置いて、テストの設計とスケーリングを行う必要があります。

SNAT ポート不足を引き起こしやすい状態を軽減するパターンについては、[SNAT の管理](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)に関するセクションを確認してください。

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>ポート マスカレード SNAT (PAT) 用のエフェメラル ポートの事前割り当て

Azure では、アルゴリズムを使用して、割り当てられる利用可能な SNAT ポートの数が決定されます。これは、ポート マスカレード SNAT ([PAT](#pat)) を使用する際のバックエンド プールのサイズに基づきます。 SNAT ポートは、特定のパブリック IP ソース アドレスについて使用可能なエフェメラル ポートです。 ロード バランサーに関連付けられている各パブリック IP アドレスでは、各 IP トランスポート プロトコルに対して 64,000 のポートを SNAT ポートとして使用できます。

同じ数の SNAT ポートが UDP と TCP にそれぞれ事前に割り当てられ、IP トランスポート プロトコルに従って個別に使用されます。  ただし、SNAT ポートの使用方法は、フローが UDP または TCP のどちらかに応じて異なります。

>[!IMPORTANT]
>Standard SKU の SNAT プログラミングは、IP トランスポート プロトコルに従い、負荷分散規則から派生します。  TCP 負荷分散規則だけが存在する場合、SNAT は TCP でのみ使用できます。 TCP 負荷分散規則しかないときに、UDP の送信 SNAT が必要な場合は、同じフロントエンドから同じバックエンド プールへの UDP 負荷分散規則を作成します。  これにより、UDP の SNAT プログラミングがトリガーされます。  動作規則や正常性プローブは不要です。  Basic SKU の SNAT では、負荷分散規則で指定されたトランスポート プロトコルに関係なく、常に両方の IP トランスポート プロトコルに対して SNAT がプログラムされます。

SNAT ポートは Azure によって各 VM の NIC の IP 構成に事前に割り当てられます。 IP 構成がプールに追加されると、バックエンド プール サイズに基づいて SNAT ポートがこの IP 構成に事前割り当てされます。 送信フローが作成されると、これらのポートは (事前に割り当てられた上限に達するまで) [PAT](#pat) によって動的に消費されます。そして、フローが終了するか[アイドル タイムアウト](../load-balancer/troubleshoot-outbound-connection.md#idletimeout)が発生すると解放されます。

次の表は、バックエンド プール サイズのレベルに対する SNAT ポートの事前割り当てを示しています。

| プール サイズ (VM インスタンス) | IP 構成あたりの事前に割り当てられる SNAT ポート|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

>[!NOTE]
> [複数フロントエンド](load-balancer-multivip-overview.md)で Standard Load Balancer を使用する場合、フロントエンド IP アドレスごとに、前の表に記載されている使用可能な SNAT ポート数を増やします。 たとえば、それぞれに個別のフロントエンド IP アドレスがある 2 つの負荷分散規則を持つ、50 個の VM のバックエンド プールでは、規則ごとに 2048 (2x 1024) 個の SNAT ポートを使用します。 詳細については、[複数のフロントエンド](#multife)に関するセクションを参照してください。

利用できる SNAT ポートの数が、そのままフローの数に変換されるわけではないことに注意してください。 複数の一意の送信先に単一の SNAT ポートを再利用できます。 ポートは、フローを一意にするために必要な場合にのみ消費されます。 設計と軽減策のガイダンスについて、[この有限のリソースを管理する方法](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust)に関するセクション、および [PAT](#pat) について説明しているセクションを参照してください。

パックエンド プールのサイズを変更すると、確立されたフローの一部に影響が及ぶ可能性があります。 バックエンド プール サイズが増加し、次のレベルに移行すると、1 つ大きいバックエンド プール レベルに移行する間に、事前に割り当てられた SNAT ポートの半分が回収されます。 回収された SNAT ポートに関連付けられているフローはタイムアウトになるので、再確立する必要があります。 新しいフローを試みると、事前に割り当てられたポートが使用可能な限り、フローはすぐに成功します。

バックエンド プールのサイズが減少し、1 つ小さいレベルに移行すると、使用できる SNAT ポートが増えます。 この場合、割り当てられている既存の SNAT ポートと各フローに影響はありません。

SNAT ポートの割り当ては、IP トランスポート プロトコル固有であり (TCP と UDP は別々に管理されます)、次の条件下で解放されます。

### <a name="tcp-snat-port-release"></a>TCP SNAT ポートの解放

- サーバー/クライアントのどちらかが FINACK を送信すると、SNAT ポートは 240 秒後に解放されます。
- RST が送信されると、SNAT ポートは 15 秒後に解放されます。
- アイドル タイムアウトに達すると、ポートは解放されます。

### <a name="udp-snat-port-release"></a>UDP SNAT ポートの解放

- アイドル タイムアウトに達すると、ポートは解放されます。

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>VM で使用されるパブリック IP の検出
送信接続のパブリック ソース IP アドレスを判別する方法は多数あります。 OpenDNS によって提供されるサービスで、VM のパブリック IP アドレスを表示できます。 

nslookup コマンドを使用することで、名前 myip.opendns.com に関する DNS クエリを OpenDNS Resolver に送信できます。 このサービスは、クエリの送信に使用されたソース IP アドレスを返します。 VM から次のクエリを実行すると、その VM で使用されるパブリック IP が応答として返されます。

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>送信接続の防止
場合によっては、VM で送信フローを作成できるのは望ましくない場合があります。 また、送信フローで接続できる宛先や受信フローを開始できる宛先を管理するための要件がある場合もあります。 このような場合は、[ネットワーク セキュリティ グループ](../virtual-network/security-overview.md) を使用すると、VM が接続できる宛先を管理できます。 また、NSG を使用して、受信フローを開始できるパブリックの宛先を管理することもできます。

負荷分散された VM に NSG を適用するときは、[サービス タグ](../virtual-network/security-overview.md#service-tags)と[既定のセキュリティ規則](../virtual-network/security-overview.md#default-security-rules)に注意してください。 VM が Azure Load Balancer からのヘルス プローブ要求を受け取ることができるようにしておいてください。 

NSG が AZURE_LOADBALANCER 既定タグからのヘルス プローブ要求をブロックすると、VM のヘルス プローブが失敗して VM が停止しているとマークされます。 ロード バランサーはその VM への新しいフローの送信を停止します。

## <a name="connections-to-azure-storage-in-the-same-region"></a>同じリージョン内の Azure Storage への接続

上記のシナリオでの送信接続の使用では、VM と同じリージョン内の Storage に接続する必要はありません。 これを望まない場合は、前述のようにネットワーク セキュリティ グループ (NSG) を使用します。 他のリージョン内の Storage への接続では、送信接続が必要です。 同じリージョン内の VM から Storage に接続する場合、Storage 診断ログ内のソース IP アドレスは、VM のパブリック IP アドレスではなく、内部プロバイダー アドレスになることに注意してください。 お使いの Storage アカウントへのアクセスを、同じリージョン内の 1 つ以上の仮想ネットワーク サブネット内の VM に制限する場合は、ストレージ アカウントのファイアウォールを構成するときに、パブリック IP アドレスではなく、[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を使用します。 サービス エンドポイントを構成すると、診断ログには、内部プロバイダー アドレスではなく、お使いの仮想ネットワークのプライベート IP アドレスが表示されます。

## <a name="azure-load-balancer-outbound-rules"></a><a name="outboundrules"></a>Azure Load Balancer のアウトバウンド規則

Azure Load Balancer は、アウトバウンドだけでなく、仮想ネットワークからのインバウンド接続も提供します。  アウトバウンド規則を使用すると、パブリック [Standard Load Balancer](load-balancer-standard-overview.md) のアウトバウンド ネットワーク アドレス変換の構成を簡素化できます。  アウトバウンド接続を宣言的に完全制御することで、特定のニーズに合わせてこの機能をスケーリングおよび調整できます。

![Load Balancer のアウトバウンド規則](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

アウトバウンド規則を使用すると、Load Balancer を使って以下を行うことができます。 
- アウトバウンド NAT をゼロから定義する。
- 既存のアウトバウンド NAT の動作をスケーリング、調整する。 

アウトバウンド規則によって以下を制御できます。
- どの仮想マシンをどのパブリック IP アドレスに変換するか。 
- [アウトバウンド SNAT ポート](load-balancer-outbound-connections.md#snat)を割り当てる方法。
- アウトバウンド変換の提供対象となるプロトコル。
- アウトバウンド接続のアイドル タイムアウトの時間 (4 ～ 120 分)。
- アイドル タイムアウト時に TCP リセットを送信するかどうか。

アウトバウンド規則により、[アウトバウンド接続](load-balancer-outbound-connections.md)に関する記事に記載されている[シナリオ 2](load-balancer-outbound-connections.md#lb) がスケーリングされます。シナリオの優先順位はそのままです。

### <a name="outbound-rule"></a>送信規則

すべてのロード バランサー規則と同様に、アウトバウンド規則は、負荷分散規則およびアウトバウンド NAT 規則と同じ構文に従います。

**フロントエンド** + **パラメーター** + **バックエンド プール**

アウトバウンド規則では、"_フロントエンド_" に変換される、"_バックエンド プールによって識別されるすべての仮想マシン_" のアウトバウンド NAT を構成します。  また、"_パラメーター_" により、アウトバウンド NAT アルゴリズムをさらに細かく制御できます。

API バージョン "2018-07-01" では、次のような構造のアウトバウンド規則定義が許可されています。

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>有効なアウトバウンド NAT 構成は、すべてのアウトバウンド規則と負荷分散規則を合成したものです。 アウトバウンド規則は負荷分散規則に対してインクリメントされます。 VM に複数の規則を適用するときに、効果的なアウトバウンド NAT 変換を管理するために、[負荷分散規則のアウトバウンド NAT を無効にする方法](#disablesnat)を確認してください。 負荷分散規則と同じパブリック IP アドレスを使用するアウトバウンド規則を定義するときは、[アウトバウンド SNAT を無効にする](#disablesnat)必要があります。

#### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>複数の IP アドレスでアウトバウンド NAT をスケーリングする

アウトバウンド規則は 1 つのパブリック IP アドレスでのみ使用できますが、アウトバウンド規則によって、アウトバウンド NAT をスケーリングするための構成の負担が軽減されます。 複数の IP アドレスを使用することで、大規模なシナリオを計画できます。また、アウトバウンド規則を使用して、[SNAT が枯渇](troubleshoot-outbound-connection.md#snatexhaust)しがちなパターンを緩和することもできます。  

フロントエンドによって提供される追加の IP アドレスごとに、Load Balancer が SNAT ポートとして使用する 64,000 個のエフェメラル ポートが提供されます。 負荷分散規則またはアウトバウンド NAT 規則では単一のフロントエンドを使用しますが、アウトバウンド規則ではフロントエンドの概念をスケーリングし、規則ごとに複数のフロントエンドを使用できます。  規則ごとに複数のフロントエンドを使用すると、使用可能な SNAT ポートの数にパブリック IP アドレスの数が乗算されるので、大規模なシナリオをサポートできます。

さらに、[パブリック IP プレフィックス](https://aka.ms/lbpublicipprefix)をアウトバウンド規則で直接使用することもできます。  パブリック IP プレフィックスの使用により、Azure デプロイからのフローのスケーリングが容易になり、ホワイトリスト登録が簡素化されます。 パブリック IP アドレス プレフィックスを直接参照するように、Load Balancer リソース内のフロントエンド IP 構成を設定できます。  これにより、Load Balancer はそのパブリック IP プレフィックスを排他的に制御することが可能になります。また、アウトバウンド規則で、パブリック IP プレフィックスに含まれるすべてのパブリック IP アドレスが自動的にアウトバウンド接続に使用されるようになります。  パブリック IP プレフィックスの範囲内の IP アドレスごとに、Load Balancer が SNAT ポートとして使用する 64,000 個のエフェメラル ポートが提供されます。   

アウトバウンド規則によって、パブリック IP プレフィックスを完全に制御する必要があるため、このオプションを使用するときは、パブリック IP プレフィックスから作成される個々のパブリック IP アドレス リソースを使用することはできません。  より細かい制御が必要な場合は、パブリック IP プレフィックスから個々のパブリック IP アドレス リソースを作成し、複数のパブリック IP アドレスをアウトバウンド規則のフロントエンドに個別に割り当てます。

#### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>SNAT ポートの割り当てを調整する

アウトバウンド規則を使用して、[バックエンド プール サイズに基づく SNAT ポートの自動割り当て](load-balancer-outbound-connections.md#preallocatedports)を調整し、SNAT ポートの自動割り当てによって提供されるポート数よりも多くのポートを割り当てたり、割り当て数を減らしたりできます。

VM (NIC IP 構成) ごとに 10,000 個の SNAT ポートを割り当てるには、次のパラメーターを使用します。
 

          "allocatedOutboundPorts": 10000

アウトバウンド規則のすべてのフロントエンドのパブリック IP アドレスごとに、SNAT ポートとして使用する最大 64,000 個のエフェメラル ポートが提供されます。  Load Balancer は、SNAT ポートを 8 の倍数で割り当てます。 8 で割り切れない値を指定すると、その構成操作は拒否されます。  パブリック IP アドレスの数に基づく使用可能な SNAT ポートの数よりも多くのポートを割り当てようとすると、その構成操作は拒否されます。  たとえば、VM ごとに 10,000 個のポートを割り当て、バックエンド プール内の 7 つの VM が 1 つのパブリック IP アドレスを共有している場合、この構成は拒否されます (7 x 10,000 SNAT ポート > 64,000 SNAT ポート)。  このシナリオに対応するには、アウトバウンド規則のフロントエンドにパブリック IP アドレスを追加します。

ポート数に 0 を指定することで、[バックエンド プール サイズに基づく SNAT ポートの自動割り当て](load-balancer-outbound-connections.md#preallocatedports)に戻すことができます。 その場合、表に従って、最初の 50 VM インスタンスは 1024 ポートを取得し、51 から 100 の VM インスタンスは、512 ポートを取得するというようになります。

#### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>アウトバウンド フローのアイドル タイムアウトを制御する

アウトバウンド規則には、アウトバウンド フローのアイドル タイムアウトを制御し、アプリケーションのニーズに合わせるための構成パラメーターがあります。  アウトバウンド アイドル タイムアウトの既定値は 4 分です。  このパラメーターには、特定の規則に一致するフローのアイドル タイムアウトの分数として、4 から 120 の値を指定できます。

アウトバウンド アイドル タイムアウトを 1 時間に設定するには、次のパラメーターを使用します。

          "idleTimeoutInMinutes": 60

#### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a> <a name="tcpreset"></a>アイドル タイムアウト時の TCP リセットを有効にする

Load Balancer の既定の動作では、アウトバウンド アイドル タイムアウトに達すると、警告なしにフローが破棄されます。  enableTCPReset パラメーターを使用すると、アプリケーションのより予測可能な動作を有効にし、アウトバウンド アイドル タイムアウトに達したときに、双方向 TCP リセット (TCP RST) を送信するかどうかを制御できます。 

アウトバウンド規則で TCP リセットを有効にするには、次のパラメーターを使用します。

           "enableTcpReset": true

利用可能なリージョンなど、詳細については、[アイドル タイムアウト時の TCP リセット](https://aka.ms/lbtcpreset)に関するページをご覧ください。

#### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>1 つの規則で TCP と UDP の両方のトランスポート プロトコルをサポートする

ほとんどの場合、アウトバウンド規則のトランスポート プロトコルには "All" を使用しますが、必要に応じて、アウトバウンド規則を特定のトランスポート プロトコルに適用することもできます。

プロトコルを TCP と UDP に設定するには、次のパラメーターを使用します。

          "protocol": "All"

#### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>負荷分散規則のアウトバウンド NAT を無効にする

以前に説明したように、負荷分散規則では送信 NAT の自動プログラミングを提供します。 ただし、負荷分散規則による送信 NAT の自動プログラミングを無効にして、アウトバウンド NAT の動作を制御または調整できるようにすることでメリットが得られるシナリオやこれが必要なシナリオもあります。  アウトバウンド規則には、アウトバウンド NAT の自動プログラミングを停止することが重要となるシナリオがあります。

このパラメーターには、次の 2 通りの使い方があります。
- アウトバウンド NAT でのアウトバウンド IP アドレスの使用を必要に応じて抑制します。  アウトバウンド規則は負荷分散規則に対してインクリメントされますが、このパラメーターを設定することで、アウトバウンド規則による制御が可能になります。
  
- インバウンドとアウトバウンドに同時に使用される IP アドレスのアウトバウンド NAT パラメーターを調整します。  アウトバウンド規則による制御を可能にするには、アウトバウンド NAT の自動プログラミングを無効にする必要があります。  たとえば、インバウンドにも使用されるアドレスの SNAT ポートの割り当てを変更するには、このパラメーターを true に設定する必要があります。  アウトバウンド規則を使用して、インバウンドにも使用される IP アドレスのパラメーターを再定義しようとしたときに、負荷分散規則のアウトバウンド NAT プログラミングを解除していない場合、アウトバウンド規則の構成操作は失敗します。

>[!IMPORTANT]
> このパラメーターを true に設定し、アウトバウンド接続を定義するアウトバウンド規則 (または[インスタンス レベルのパブリック IP のシナリオ](load-balancer-outbound-connections.md#ilpip)) がない場合、仮想マシンはアウトバウンド接続できなくなります。  VM またはアプリケーションの一部の操作は、アウトバウンド接続が使用可能であることに依存している場合があります。 使用しているシナリオの依存関係を理解し、この変更による影響を考慮してください。

負荷分散規則で送信 SNAT を無効にするには、次の構成パラメーターを使用します。

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

disableOutboundSnat パラメーターの既定値は false です。この場合、負荷分散規則は、負荷分散規則構成のミラー イメージとして自動アウトバウンド NAT を提供**します**。  

負荷分散規則で disableOutboundSnat を true に設定すると、負荷分散規則はアウトバウンド NAT の自動プログラミングの制御を解放します。  負荷分散規則の結果としてのアウトバウンド SNAT は無効になります。

#### <a name="reuse-existing-or-define-new-backend-pools"></a>既存のバックエンド プールを再利用するか、新しいバックエンド プールを定義する

アウトバウンド規則では、規則を適用する VM のグループを定義するための新しい概念は導入されていません。  代わりに、負荷分散規則にも使用されるバックエンド プールの概念を再利用します。 これを使用して、既存のバックエンド プール定義を再利用するか、アウトバウンド規則専用のものを作成することで、構成を簡素化できます。

### <a name="scenarios"></a>シナリオ

#### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>アウトバウンド接続をパブリック IP アドレスの特定のセットに調整する

アウトバウンド規則を使用して、パブリック IP アドレスの特定のセットが発信元になるようにアウトバウンド接続を調整し、ホワイトリスト登録のシナリオを容易にすることができます。  この発信元パブリック IP アドレスは、負荷分散規則で使用されているものと同じであっても、負荷分散規則で使用されているものとは異なるパブリック IP アドレスのセットであっても構いません。  

1. [パブリック IP プレフィックス](https://aka.ms/lbpublicipprefix) (またはパブリック IP プレフィックスのパブリック IP アドレス) を作成します。
2. パブリック Standard Load Balancer を作成する
3. 使用するパブリック IP プレフィックス (またはパブリック IP アドレス) を参照するフロントエンドを作成します。
4. バックエンド プールを再利用するか、バックエンド プールを作成し、VM をパブリック Load Balancer のバックエンド プールに配置します。
5. フロントエンドを使用してこれらの VM のアウトバウンド NAT をプログラムするように、パブリック Load Balancer のアウトバウンド規則を構成します。
   
アウトバウンドに負荷分散規則を使用しない場合は、負荷分散規則で[アウトバウンド SNAT を無効にする](#disablesnat)必要があります。

#### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>SNAT ポートの割り当てを変更する

アウトバウンド規則を使用して、[バックエンド プール サイズに基づく SNAT ポートの自動割り当て](load-balancer-outbound-connections.md#preallocatedports)を調整できます。

たとえば、アウトバウンド NAT の 1 つのパブリック IP アドレスを共有する 2 つの仮想マシンがあり、SNAT ポートが枯渇している場合は、割り当てられた SNAT ポートの数を既定の 1024 個から増やすことができます。 パブリック IP アドレスごとに、最大 64,000 個のエフェメラル ポートを提供できます。  単一のパブリック IP アドレス フロントエンドでアウトバウンド規則を構成した場合、合計 64,000 個の SNAT ポートをバックエンド プール内の VM に配布できます。  2 つの VM の場合、アウトバウンド規則を使用して最大 32,000 個 (2 x 32,000 = 64,000) の SNAT ポートを割り当てることができます。

[アウトバウンド接続](load-balancer-outbound-connections.md)の概要と、[SNAT](load-balancer-outbound-connections.md#snat) ポートを割り当てて使用する方法の詳細をご覧ください。

#### <a name="enable-outbound-only"></a><a name="outboundonly"></a>アウトバウンドのみを有効にする

パブリック Standard Load Balancer を使用して、VM のグループにアウトバウンド NAT を提供できます。 このシナリオでは、追加の規則を必要とせずに、アウトバウンド規則を単独で使用できます。

##### <a name="outbound-nat-for-vms-only-no-inbound"></a>VM のアウトバウンド NAT のみ (インバウンドなし)

パブリック Standard Load Balancer を定義し、VM をバックエンド プールに配置します。アウトバウンド NAT をプログラムするようにし、特定のパブリック IP アドレスが発信元になるようにアウトバウンド接続を調整するアウトバウンド規則を構成します。 パブリック IP プレフィックスを使用して、アウトバウンド接続の発信元のホワイトリスト登録を簡素化することもできます。

1. パブリック Standard Load Balancer を作成します。
2. バックエンド プールを作成し、VM をパブリック Load Balancer のバックエンド プールに配置します。
3. これらの VM のアウトバウンド NAT をプログラムするように、パブリック Load Balancer のアウトバウンド規則を構成します。

##### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>内部 Standard Load Balancer のアウトバウンド NAT のシナリオ

内部 Standard Load Balancer を使用する場合、送信接続が明示的に宣言されるまで送信 NAT は使用できません。 以下の手順で、アウトバウンド規則を使用して内部 Standard Load Balancer の背後にある VM の送信接続を作成し、送信接続を定義します。

1. パブリック Standard Load Balancer を作成します。
2. 内部 Load Balancer に加えて、バックエンド プールを作成し、VM をパブリック Load Balancer のバックエンド プールに配置します。
3. これらの VM のアウトバウンド NAT をプログラムするように、パブリック Load Balancer のアウトバウンド規則を構成します。

##### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>パブリック Standard Load Balancer でアウトバウンド NAT の TCP プロトコルと UDP プロトコルの両方を有効にする

- パブリック Standard Load Balancer を使用する場合、提供されるアウトバウンド NAT の自動プログラミングは負荷分散規則のトランスポート プロトコルに対応します。  

   1. 負荷分散規則でアウトバウンド SNAT を無効にします。
   2. 同じ Load Balancer のアウトバウンド規則を構成します。
   3. VM によって既に使用されているバックエンド プールを再利用します。
   4. アウトバウンド規則の一部として "protocol": "All" を指定します。

- インバウンド NAT 規則のみを使用している場合、アウトバウンド NAT は提供されません。

   1. VM をバックエンド プールに配置します。
   2. パブリック IP アドレスまたはパブリック IP プレフィックスを使用して、1 つまたは複数のフロントエンド IP 構成を定義します。
   3. 同じ Load Balancer のアウトバウンド規則を構成します。
   4. アウトバウンド規則の一部として "protocol": "All" を指定します


## <a name="limitations"></a>制限事項
- フロントエンド IP アドレスごとに使用可能なエフェメラル ポートの最大数は 64,000 個です。
- 構成可能なアウトバウンド アイドル タイムアウトの範囲は、4 から 120 分 (240 から 7200 秒) です。
- Load Balancer では、アウトバウンド NAT の ICMP はサポートされていません。
- アウトバウンド規則は、NIC のプライマリ IP 構成にのみ適用できます。  複数 NIC がサポートされています。
- VNet およびその他の Microsoft プラットフォーム サービスなしの Web Worker ロールにアクセスできるのは、事前 VNet サービスおよびその他のプラットフォーム サービスの動作の副作用により、内部の Standard Load Balancer が使用される場合のみです。 それぞれのサービス自体、または基になるプラットフォームは予告なく変更されることがあるため、この副作用に依存しないでください。 内部の Standard Load Balancer のみを使用する場合は、必要に応じて、明示的に送信接続を作成する必要があることを常に想定する必要があります。 この記事で説明されている[既定の SNAT](#defaultsnat) のシナリオ 3 を使用することはできません。

## <a name="next-steps"></a>次のステップ

- [Standard Load Balancer](load-balancer-standard-overview.md) の詳細を確認する。
- Standard パブリック Load Balancer の[アウトバウンド規則](load-balancer-outbound-rules-overview.md)の詳細を確認する。
- [Load Balancer](load-balancer-overview.md) について詳しく学習する。
- [ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)の詳細を確認する。
- Azure のその他の重要な[ネットワーク機能](../networking/networking-overview.md)について参照してください。
