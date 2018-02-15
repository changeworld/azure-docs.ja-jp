---
title: "Azure Stack 統合システムの境界接続のネットワーク統合に関する考慮事項 | Microsoft Docs"
description: "マルチノードの Azure Stack とデータセンターの境界ネットワーク接続を計画するためにできることを説明します。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 93dd609df90adac2c84ba8c62cf0d18f55a317bb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="border-connectivity"></a>境界接続 
ネットワーク統合の計画は、Azure Stack 統合システムの展開、操作、および管理を正常に行うための重要な前提条件です。 境界接続の計画は、境界ゲートウェイ プロトコル (BGP) による動的ルーティングを使用するかどうかを選択することから始まります。 これを行うには、16 ビットの BGP 自律システム番号 (パブリックまたはプライベート) を割り当てるか、または既定の静的ルートが境界デバイスに割り当てられる静的ルーティングを使用する必要があります。

> [!IMPORTANT]
> Top-of-Rack (TOR) スイッチでは、ポイント ツー ポイント IP (/30 ネットワーク) を持つレイヤー 3 アップリンクが物理インターフェイスに構成されている必要があります。 Azure Stack 操作をサポートする TOR スイッチにレイヤー 2 アップリンクを使用することはサポートされていません。 

## <a name="bgp-routing"></a>BGP ルーティング
BGP のような動的ルーティング プロトコルを使用すると、システムが常にネットワークの変更を把握していることが保証され、管理が容易になります。 

次の図に示すように、TOR スイッチ上のプライベート IP 空間の公開は、prefix-list を使用して制限されます。 このプレフィックスの一覧により、プライベート IP サブネットとそれを TOR と境界間の接続にルート マップとして適用することが拒否されます。

Azure Stack ソリューションの内部で実行されている ソフトウェア ロード バランサー (SLB) は、TOR デバイスをピアにして、VIP アドレスを動的に公開できるようにします。

ユーザー トラフィックが即時かつ透過的に障害から復旧できるようにするために、TOR デバイス間で構成された VPC または MLAG は、ホストと IP ネットワークの冗長性を提供する HSRP または VRRP に対してマルチシャーシ リンク アグリゲーションの使用を許可します。

![BGP ルーティング](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>静的ルーティング
静的ルートを使用すると、より固定された構成が境界と TOR デバイスに追加されます。 これは、あらゆる変更の前に徹底的な分析を要求します。 構成エラーによって発生した問題の場合、行われた変更によっては、より長い時間がかかる可能性があります。 ルーティング メソッドは推奨されていませんが、サポートはされています。

このメソッドを使用して Azure Stack をネットワーク環境に統合するには、境界デバイスに、外部ネットワーク (パブリック VIP) 宛てのトラフィック用の TOR デバイスをポイントしている静的ルートが構成されている必要があります。

TOR デバイスには、すべてのトラフィックを境界デバイスに送信する既定の静的ルートが構成されている必要があります。 このルールに対する 1 つのトラフィックの例外は、TOR から境界への接続に適用されたアクセス制御リストを使用してブロックされるプライベート空間の場合です。

それ以外のすべての場合は、最初のメソッドと同じである必要があります。 BGP 動的ルーティングはラックの内部でも使用されます。これは SLB とその他のコンポーネントにとって不可欠なツールであり、無効にしたり削除したりはできないためです。

![静的ルーティング](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>透過プロキシ
データセンターですべてのトラフィックがプロキシを使用する必要がある場合は、ラックからのすべてのトラフィックをポリシーに従って処理し、ネットワーク上のゾーン間でトラフィックを分離する*透過プロキシ*を構成する必要があります。

> [!IMPORTANT]
> Azure Stack ソリューションは、通常の Web プロキシをサポートしていません。  

透過プロキシ (インターセプト、インライン、または強制プロキシとも呼ばれます) は、特殊なクライアント構成を必要とすることなく、通常の通信をネットワーク レイヤーでインターセプトします。 クライアントがプロキシの存在を意識する必要はありません。

![透過プロキシ](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>次の手順
[DNS の統合](azure-stack-integrate-dns.md)