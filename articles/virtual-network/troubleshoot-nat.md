---
title: Azure Virtual Network NAT 接続に関する問題のトラブルシューティング
titleSuffix: Azure Virtual Network NAT troubleshooting
description: Virtual Network NAT の問題のトラブルシューティングを行います。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: d56cd3b3d286d69a51d8cc14eb8020343cf7295a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302986"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Azure Virtual Network NAT 接続に関する問題のトラブルシューティング

この記事は、管理者が Virtual Network NAT を使用する際の接続の問題を診断および解決する場合に役立ちます。

>[!NOTE] 
>Virtual Network NAT は、現時点ではパブリック プレビューとして提供されています。 現在は、ご利用いただける[リージョン](nat-overview.md#region-availability)が限られています。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms)」をご覧ください。

## <a name="problems"></a>問題が発生した場合

- [SNAT の枯渇](#snat-exhaustion)。
- [ICMP ping が失敗する](#icmp-ping-is-failing)。

これらの問題を解決するには、次のセクションの手順に従ってください。

## <a name="resolution"></a>解像度

### <a name="snat-exhaustion"></a>SNAT の枯渇

1 つの [NAT ゲートウェイ リソース](nat-gateway-resource.md)で、64,000 から最大 100 万件の同時フローがサポートされます。  各 IP アドレスでは、64,000 個の SNAT ポートが使用可能なインベントリに提供されます。 NAT ゲートウェイ リソースあたり最大 16 個の IP アドレスを使用できます。  SNAT のメカニズムについては、[こちら](nat-gateway-resource.md#source-network-address-translation)で詳しく説明されています。

#### <a name="steps"></a>手順:

1. 対象のアプリケーションで送信接続をどのように作成しているかを調査します (たとえば、コード レビューやパケット キャプチャ)。 
2. このアクティビティが予期される動作であるかどうか、またはアプリケーションが誤動作しているかどうかを判断します。  Azure Monitor で[メトリック](nat-metrics.md)を使用して、調査結果を実証します。
3. 適切なパターンに従っているかどうかを評価します。
4. NAT ゲートウェイ リソースに割り当てられた追加の IP アドレスを使用して SNAT ポートの枯渇を軽減する必要があるかどうかを評価します。

#### <a name="design-pattern"></a>設計パターン:

可能な場合は常に、接続の再利用と接続プールを利用してください。  このパターンは、リソース枯渇の問題を完全に回避し、予測可能な動作をもたらします。 これらのパターンのプリミティブは、多くの開発ライブラリとフレームワークに含まれています。
- 実行時間の長い操作に対しては、[非同期ポーリング パターン](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply)を使用して他の操作のために接続リソースを解放することを検討します。
- 有効期間の長いフロー (たとえば、再利用された TCP 接続) では、中間システムのタイムアウトを回避するために、TCP キープアライブまたはアプリケーション レイヤー キープアライブを使用する必要があります。
- 一時的な障害または障害復旧中の積極的な再試行またはバーストを回避するには、グレースフルな[再試行パターン](https://docs.microsoft.com/azure/architecture/patterns/retry)を使用する必要があります。
すべての HTTP 操作に対して新しい TCP 接続を作成することはアンチパターンです ("アトミック接続" とも呼ばれます)。  アトミック接続は、アプリケーションのスケーリングを妨げ、リソースを浪費します。  常に複数の操作をパイプラインで同じ接続に渡してください。  アプリケーションにとっては、トランザクション速度とリソース コストの面でメリットがあります。  アプリケーションでトランスポート レイヤー暗号化 (TLS など) を使用する場合、新しい接続の処理に関連する多大なコストがかかります。  その他のベスト プラクティス パターンについては、[Azure のクラウド設計パターン](https://docs.microsoft.com/azure/architecture/patterns/)に関するページを確認してください。

#### <a name="mitigations"></a>軽減策

送信接続は、次のようにスケーリングできます。

| シナリオ | 対応策 |
|---|---|
| 使用率が高い期間に SNAT ポートの競合と SNAT ポートの枯渇が発生している。 | パブリック IP アドレス リソースまたはパブリック IP プレフィックス リソースをさらに追加できるかどうかを判断します。 この追加により、NAT ゲートウェイに対して合計で最大 16 個の IP アドレスを使用できるようになります。 この追加により、使用可能な SNAT ポートのインベントリが増え (IP アドレスあたり 64,000 個)、シナリオをさらにスケーリングできるようになります。|
| 既に 16 個の IP アドレスを取得したが、SNAT ポートの枯渇が引き続き発生する。 | アプリケーション環境を複数のサブネットに分散し、各サブネットに NAT ゲートウェイ リソースを提供します。 |

>[!NOTE]
>SNAT の枯渇が発生する理由を理解することが重要です。 スケーラブルで信頼性の高いシナリオに適したパターンを使用していることを確認してください。  需要の原因を理解できないままさらに多くの SNAT ポートをシナリオに追加することは最後の手段にする必要があります。 現在のシナリオで SNAT ポート インベントリに負荷がかかっている理由を理解できない場合、IP アドレスを追加することで SNAT ポートをインベントリに追加しても、アプリケーションをスケーリングしたときに同じ枯渇の障害が発生するのを遅らせるだけです。  他の非効率性やアンチパターンを隠すことになります。

### <a name="icmp-ping-is-failing"></a>ICMP ping が失敗する

[Virtual Network NAT](nat-overview.md) では、IPv4 UDP および TCP プロトコルがサポートされています。 ICMP はサポートされておらず、失敗することが予想されます。  代わりに、TCP 接続テスト ("TCP ping" など) と UDP 固有のアプリケーション レイヤー テストを使用して、エンド ツー エンドの接続を検証します。

次の表は、テストを開始するために使用するツールの出発点として使用できます。

| オペレーティング システム | 汎用 TCP 接続テスト | TCP アプリケーション レイヤー テスト | UDP |
|---|---|---|---|
| Linux | nc (汎用接続テスト) | curl (TCP アプリケーション レイヤー テスト) | アプリケーション固有 |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | アプリケーション固有 |

## <a name="next-steps"></a>次のステップ

- [Virtual Network NAT](nat-overview.md) について学習する。
- [NAT ゲートウェイ リソース](nat-gateway-resource.md)について学習する
- [NAT ゲートウェイ リソースのメトリックとアラート](nat-metrics.md)について学習する。
