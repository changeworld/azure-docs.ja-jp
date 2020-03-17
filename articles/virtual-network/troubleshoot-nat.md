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
ms.date: 03/05/2020
ms.author: allensu
ms.openlocfilehash: c629b3425cd095a6ac9d305b5cd6de58ed9d572a
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674327"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Azure Virtual Network NAT 接続に関する問題のトラブルシューティング

この記事は、管理者が Virtual Network NAT を使用する際の接続の問題を診断および解決する場合に役立ちます。

>[!NOTE] 
>Virtual Network NAT は、現時点ではパブリック プレビューとして提供されています。 現在は、ご利用いただける[リージョン](nat-overview.md#region-availability)が限られています。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms)」をご覧ください。

## <a name="problems"></a>問題が発生した場合

* [SNAT の枯渇](#snat-exhaustion)
* [ICMP ping が失敗する](#icmp-ping-is-failing)
* [接続エラー](#connectivity-failures)
* [IPv6 の共存](#ipv6-coexistence)

これらの問題を解決するには、次のセクションの手順に従ってください。

## <a name="resolution"></a>解像度

### <a name="snat-exhaustion"></a>SNAT の枯渇

1 つの [NAT ゲートウェイ リソース](nat-gateway-resource.md)で、64,000 から最大 100 万件の同時フローがサポートされます。  各 IP アドレスでは、64,000 個の SNAT ポートが使用可能なインベントリに提供されます。 NAT ゲートウェイ リソースあたり最大 16 個の IP アドレスを使用できます。  SNAT のメカニズムについては、[こちら](nat-gateway-resource.md#source-network-address-translation)で詳しく説明されています。

多くの場合、SNAT が枯渇する根本的な原因は、アウトバウンド接続の確立方法と管理方法のアンチパターンです。  このセクションの内容を慎重に検討してください。

#### <a name="steps"></a>手順

1. 対象のアプリケーションで送信接続をどのように作成しているかを調査します (たとえば、コード レビューやパケット キャプチャ)。 
2. このアクティビティが予期される動作であるかどうか、またはアプリケーションが誤動作しているかどうかを判断します。  Azure Monitor で[メトリック](nat-metrics.md)を使用して、調査結果を実証します。 SNAT 接続メトリックの "失敗" カテゴリを使用してください。
3. 適切なパターンに従っているかどうかを評価します。
4. NAT ゲートウェイ リソースに割り当てられた追加の IP アドレスを使用して SNAT ポートの枯渇を軽減する必要があるかどうかを評価します。

#### <a name="design-patterns"></a>設計パターン

可能な場合は常に、接続の再利用と接続プールを利用してください。  これらのパターンは、リソース枯渇の問題を完全に回避し、予測可能で信頼性が高く、スケーラブルな動作をもたらします。 これらのパターンのプリミティブは、多くの開発ライブラリとフレームワークに含まれています。

_**解決方法:**_ 適切なパターンを使用します。

- 実行時間の長い操作に対しては、[非同期ポーリング パターン](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply)を使用して他の操作のために接続リソースを解放することを検討します。
- 有効期間の長いフロー (たとえば、再利用された TCP 接続) では、中間システムのタイムアウトを回避するために、TCP キープアライブまたはアプリケーション レイヤー キープアライブを使用する必要があります。
- 一時的な障害または障害復旧中の積極的な再試行またはバーストを回避するには、グレースフルな[再試行パターン](https://docs.microsoft.com/azure/architecture/patterns/retry)を使用する必要があります。
すべての HTTP 操作に対して新しい TCP 接続を作成することはアンチパターンです ("アトミック接続" とも呼ばれます)。  アトミック接続は、アプリケーションのスケーリングを妨げ、リソースを浪費します。  常に複数の操作をパイプラインで同じ接続に渡してください。  アプリケーションにとっては、トランザクション速度とリソース コストの面でメリットがあります。  アプリケーションでトランスポート レイヤー暗号化 (TLS など) を使用する場合、新しい接続の処理に関連する多大なコストがかかります。  その他のベスト プラクティス パターンについては、[Azure のクラウド設計パターン](https://docs.microsoft.com/azure/architecture/patterns/)に関するページを確認してください。

#### <a name="possible-mitigations"></a>可能な軽減策

_**解決方法:**_ アウトバウンド接続を次のようにスケーリングします。

| シナリオ | 証拠 |対応策 |
|---|---|---|
| 使用率が高い期間に SNAT ポートの競合と SNAT ポートの枯渇が発生している。 | Azure Monitor の SNAT 接続[メトリック](nat-metrics.md)の "失敗" カテゴリに、一定期間にわたる一時的または永続的な失敗や、大量の接続が表示される。  | パブリック IP アドレス リソースまたはパブリック IP プレフィックス リソースをさらに追加できるかどうかを判断します。 この追加により、NAT ゲートウェイに対して合計で最大 16 個の IP アドレスを使用できるようになります。 この追加により、使用可能な SNAT ポートのインベントリが増え (IP アドレスあたり 64,000 個)、シナリオをさらにスケーリングできるようになります。|
| 既に 16 個の IP アドレスを取得したが、SNAT ポートの枯渇が引き続き発生する。 | 新たに IP アドレスを追加しようとすると失敗する。 パブリック IP アドレス リソースまたはパブリック IP プレフィックス リソースからの IP アドレスの総数が合計 16 個を超えている。 | アプリケーション環境を複数のサブネットに分散し、各サブネットに NAT ゲートウェイ リソースを提供します。  設計パターンを再評価し、前述の[ガイダンス](#design-patterns)に基づいて最適化します。 |

>[!NOTE]
>SNAT の枯渇が発生する理由を理解することが重要です。 スケーラブルで信頼性の高いシナリオに適したパターンを使用していることを確認してください。  需要の原因を理解できないままさらに多くの SNAT ポートをシナリオに追加することは最後の手段にする必要があります。 現在のシナリオで SNAT ポート インベントリに負荷がかかっている理由を理解できない場合、IP アドレスを追加することで SNAT ポートをインベントリに追加しても、アプリケーションをスケーリングしたときに同じ枯渇の障害が発生するのを遅らせるだけです。  他の非効率性やアンチパターンを隠すことになります。

### <a name="icmp-ping-is-failing"></a>ICMP ping が失敗する

[Virtual Network NAT](nat-overview.md) では、IPv4 UDP および TCP プロトコルがサポートされています。 ICMP はサポートされておらず、失敗することが予想されます。  

_**解決方法:**_ 代わりに、TCP 接続テスト ("TCP ping" など) と UDP 固有のアプリケーション レイヤー テストを使用して、エンド ツー エンドの接続を検証します。

次の表は、テストを開始するために使用するツールの出発点として使用できます。

| オペレーティング システム | 汎用 TCP 接続テスト | TCP アプリケーション レイヤー テスト | UDP |
|---|---|---|---|
| Linux | nc (汎用接続テスト) | curl (TCP アプリケーション レイヤー テスト) | アプリケーション固有 |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | アプリケーション固有 |

### <a name="connectivity-failures"></a>接続エラー

[Virtual Network NAT](nat-overview.md) に関する接続の問題は、いくつかの異なる問題が原因で発生します。

* NAT ゲートウェイの一時的または永続的な [SNAT の枯渇](#snat-exhaustion)。
* Azure インフラストラクチャにおける一時的な障害。 
* Azure とパブリック インターネットの宛先との間のパスに生じた一時的な障害。 
* パブリック インターネットの宛先における一時的または永続的な障害。

接続の検証には、次のようなツールを使用します。 [ICMP ping はサポートされていません](#icmp-ping-is-failing)。

| オペレーティング システム | 汎用 TCP 接続テスト | TCP アプリケーション レイヤー テスト | UDP |
|---|---|---|---|
| Linux | nc (汎用接続テスト) | curl (TCP アプリケーション レイヤー テスト) | アプリケーション固有 |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | アプリケーション固有 |

#### <a name="snat-exhaustion"></a>SNAT の枯渇

この記事の「[SNAT の枯渇](#snat-exhaustion)」セクションを参照してください。

#### <a name="azure-infrastructure"></a>Azure インフラストラクチャ

Azure はそのインフラストラクチャを細心の注意を払いながら監視、運用していますが、伝送損失がないという保証はないため、一時的な障害が発生する可能性はあります。  TCP アプリケーションには、SYN 再送を見込んだ設計パターンを使用してください。 SYN パケットの損失によって生じる一時的な影響を軽減するために、接続タイムアウトには、TCP SYN 再送ができるだけの十分な長さを確保します。

_**解決方法:**_

* [SNAT の枯渇](#snat-exhaustion)が生じていないかチェックします。
* SYN 再送の動作を制御する TCP スタックの構成パラメーターは、RTO ([Retransmission Time-Out: 再送タイムアウト](https://tools.ietf.org/html/rfc793)) と呼ばれます。 RTO の値は調整できますが、通常は 1 秒以上で、指数バックオフが既定で使用されます。  アプリケーションの接続タイムアウトが短すぎると (1 秒など)、接続タイムアウトが散発的に発生する可能性があります。  アプリケーションの接続タイムアウトを長くしてください。
* 既定のアプリケーションの動作でタイムアウトが予想以上に長いと感じられる場合は、サポート ケースを開いて詳細なトラブルシューティングを行ってください。

人為的に TCP 接続タイムアウトを短縮したり、RTO パラメーターをチューニングしたりすることはお勧めしません。

#### <a name="public-internet-transit"></a>パブリック インターネット トランジット

宛先までのパスが長くなって中間システムが増えるほど、一時的な障害が発生する確率は高くなります。 [Azure インフラストラクチャ](#azure-infrastructure)と比べ、一時的な障害の頻度は高くなることが予想されます。 

前出の「[Azure インフラストラクチャ](#azure-infrastructure)」セクションと同じガイダンスに従ってください。

#### <a name="internet-endpoint"></a>インターネット エンドポイント

通信を行うインターネット エンドポイントに関連した考慮事項に加えて、前出の各セクションが当てはまります。 その他、以下の要因も、接続の成功に影響を及ぼす可能性があります。

* 宛先側でのトラフィック管理 (以下を含む)
- 宛先側によって課される API レート制限
- Volumetric DDoS の軽減策やトランスポート層のトラフィック シェイプ
* ファイアウォールなど、宛先側のコンポーネント 

通常、発生している事象を調査するためには、送信元だけでなく宛先 (可能な場合) でのパケット キャプチャが必要です。

_**解決方法:**_

* [SNAT の枯渇](#snat-exhaustion)が生じていないかチェックします。 
* 同じリージョン (または比較のために他のリージョン) のエンドポイントとの接続を確認します。  
* 大容量または高トランザクション速度のテストを実施している場合、速度を落とすことでエラーの頻度が下がるかどうかを調べます。
* 速度を変えることでエラーの割合に変化が生じる場合は、API 速度制限など、宛先側の制約が上限に達しているかどうかを確認します。
* 調査しても結論が出ない場合は、サポート ケースを開いて詳細なトラブルシューティングを行ってください。

#### <a name="tcp-resets-received"></a>TCP リセットの受信

送信元 VM で TCP リセット (TCP RST パケット) の受信が観察される場合、それらのパケットは、処理中として認識されていないフローのプライベート側の NAT ゲートウェイによって生成されている可能性があります。  考えられる原因の 1 つは、TCP 接続のアイドル タイムアウトです。アイドル タイムアウトは、4 分から最大 120 分までの範囲で調整できます。

TCP リセットが NAT ゲートウェイ リソースのパブリック側で生成されることはありません。 TCP リセットを宛先側で受信したとしても、それらは送信元 VM のスタックで生成されたものであって、NAT ゲートウェイ リソースによって生成されたものではありません。

_**解決方法:**_

* [設計パターン](#design-patterns)の推奨事項を確認します。  
* 必要であれば、サポート ケースを開いて詳細なトラブルシューティングを行ってください。

### <a name="ipv6-coexistence"></a>IPv6 の共存

[Virtual Network NAT](nat-overview.md) は IPv4 の UDP および TCP プロトコルをサポートしており、[IPv6 プレフィックスを使用したサブネットへのデプロイはサポートされません](nat-overview.md#limitations)。

_**解決方法:**_ IPv6 プレフィックスが使用されていないサブネットに NAT ゲートウェイをデプロイしてください。

追加機能のご要望は、[Virtual Network NAT の UserVoice](https://aka.ms/natuservoice) までお寄せください。

## <a name="next-steps"></a>次のステップ

* [Virtual Network NAT](nat-overview.md) について学習する。
* [NAT ゲートウェイ リソース](nat-gateway-resource.md)について学習する
* [NAT ゲートウェイ リソースのメトリックとアラート](nat-metrics.md)について学習する。
* [UserVoice で Virtual Network NAT の新機能の構築を提案する](https://aka.ms/natuservoice)。

