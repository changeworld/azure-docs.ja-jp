---
title: Gateway Load Balancer およびパートナー NVA を使用したインライン L7 DDoS 保護
description: Gateway Load Balancer およびパートナー NVA を使用してインライン L7 DDoS 保護を作成して有効にする方法について説明します。
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.author: yitoh
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 49e4bc5dbe2214cafe039ceb0d6163a42f388629
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715323"
---
# <a name="inline-l7-ddos-protection-with-gateway-load-balancer-and-partner-nvas"></a>Gateway Load Balancer およびパートナー NVA を使用したインライン L7 DDoS 保護

Azure DDoS Protection は常に有効ですが、インラインではなく、攻撃が検出されてから軽減されるまで 30 から 60 秒かかります。 Azure DDoS Protection Standard は L3/4 (ネットワーク レイヤー) でも機能しますが、パケット ペイロード (つまりアプリケーション レイヤー (L7)) は検査しません。  
待機時間の影響を大きく受け、DDoS 保護を開始するために 30 から 60 秒のオンランプ時間を許容できないワークロードでは、インライン保護が必要です。 インライン保護では、すべてのトラフィックが常に DDoS 保護パイプラインを通過する必要があります。 さらに、Web 保護やゲーム ワークロード保護などのシナリオ (UDP) では、パケット ペイロードを検査して、アプリケーション層 (L7) の脆弱性を悪用する極端にボリュームの少ない攻撃を軽減することが重要です。 

Gateway Load Balancer と一緒にデプロイされ、Azure DDoS Protection Standard と統合されたパートナー NVA は、ハイ パフォーマンスと高可用性のシナリオに対応する包括的なインライン L7 DDoS 保護を提供します。 インライン L7 DDoS 保護を Azure DDoS Protection Standard と組み合わせると、帯域幅消費型攻撃だけでなく、ボリュームの少ない DDoS 攻撃に対しても包括的な L3-L7 保護が提供されます。 

## <a name="what-is-a-gateway-load-balancer"></a>Gateway Load Balancer とは
Gateway Load Balancer は、サードパーティのネットワーク仮想アプライアンス (NVA) を使用したハイ パフォーマンスと高可用性のシナリオに特化した、Azure Load Balancer の SKU です。

Gateway LB の機能を使用すると、NVA のデプロイ、スケーリング、管理を簡単に行えます。Gateway LB をパブリック エンドポイントにチェーンするために複雑な操作は必要ありません。  Gateway LB を使用して、ファイアウォール、高度なパケット分析、侵入検出や防止システム、またはニーズに合ったカスタム シナリオなど、さまざまなシナリオにアプライアンスを挿入できます。 NVA を使用するシナリオでは、フローが "対称" である必要があります。これにより、セッションが確実に維持され、対称的になります。 Gateway LB は、バックエンド プール内の特定のインスタンスに対してフローの対称性を維持します。

Gateway Load Balancer の詳細については、[Gateway LB](../load-balancer/gateway-overview.md) の製品とドキュメントを参照してください。 

## <a name="inline-ddos-protection-with-gateway-lb-and-partner-nvas"></a>Gateway LB とパートナー NVA を使用したインライン DDoS 保護

高待機時間の影響を受けやすいワークロード (ゲームなど) に対する DDoS 攻撃により、2 から 10 秒の範囲で停止が発生し、可用性が中断される可能性があります。 Gateway Load Balancer は、関連する NVA がインターネット トラフィックのイングレス パスに挿入されるようにすることで、このようなワークロードを保護できます。 Gateway Load Balancer が仮想マシン上の Standard Public Load Balancer フロントエンドまたは IP 構成にチェーンされた後、アプリケーション エンドポイント間のトラフィックが Azure Gateway LB に送信されるのを確認するために、追加の構成は必要はありません。 

受信トラフィックは常にパス内で NVA を介して検査され、クリーンなトラフィックがバックエンド インフラストラクチャ (ゲーム サーバー) に返されます。 

トラフィックは、コンシューマー仮想ネットワークからプロバイダー仮想ネットワークに流れ、その後コンシューマー仮想ネットワークに戻されます。 コンシューマー仮想ネットワークとプロバイダー仮想ネットワークは、異なるサブスクリプション、テナント、またはリージョンに含めることができるため、柔軟性が向上し、管理が容易になります。

![ゲートウェイ ロード バランサーによる DDoS インライン保護](./media/ddos-glb.png)
 
Standard Public Load Balancer フロントエンドの VNet または仮想マシンの VNet 上で Azure DDoS Protection Standard を有効にすると、L3/4 の DDoS 攻撃からの保護が提供されます。 
1.  インターネットからのフィルター処理されていないゲーム トラフィックは、ゲーム サーバーのロード バランサーのパブリック IP にリダイレクトされます。 
2.  フィルター処理されていないゲーム トラフィックは、チェーンされた Gateway Load Balancer のプライベート IP にリダイレクトされます。 
3.  フィルター処理されていないゲーム トラフィックでは、パートナー NVA を介して DDoS 攻撃がリアルタイムで検査されます。 
4.  フィルター処理されたゲーム トラフィックは、最終的な処理のためにゲーム サーバーに返されます。
5.  ゲーム サーバー上の Azure DDoS Protection Standard の Load Balancer は L3/4 DDoS 攻撃から保護し、DDoS 保護ポリシーは、ゲーム サーバーのトラフィック プロファイルとアプリケーションの規模に沿って自動的に調整されます。 

## <a name="next-steps"></a>次のステップ
- [インライン L7 DDoS 保護パートナー](https://aka.ms/inlineddospartners)の詳細情報
- [Azure DDoS Protection Standard](./ddos-protection-overview.md) の詳細情報
- [Gateway Load Balancer](../load-balancer/gateway-overview.md) の詳細情報