---
title: Azure Virtual Network NAT とは
description: Virtual Network NAT の機能、リソース、アーキテクチャ、実装の概要。 Virtual Network NAT の動作のしくみと、クラウドにおける NAT ゲートウェイ リソースの使用方法について説明します。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: allensu
ms.openlocfilehash: 205826a6ad952383582f5a8086cbd8b85dbc3794
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359254"
---
# <a name="what-is-virtual-network-nat-public-preview"></a>Virtual Network NAT (パブリック プレビュー) とは

Virtual Network NAT (ネットワーク アドレス変換) は、仮想ネットワーク用のアウトバウンドのみのインターネット接続を簡単に行えるようにするものです。 これをサブネットに対して構成した場合、指定した静的パブリック IP アドレスがすべてのアウトバウンド接続で使用されます。  ロード バランサーや、仮想マシンに直接アタッチされたパブリック IP アドレスがなくても、アウトバウンド接続が可能となります。 NAT はフル マネージドで、高い回復性を備えています。

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" width="256" title="Virtual Network NAT">
</p>



*図:Virtual Network NAT*


>[!NOTE] 
>Virtual Network NAT は、現時点ではパブリック プレビューとして提供されています。 現在は、ご利用いただける[リージョン](#region-availability)が限られています。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms)」をご覧ください。

## <a name="static-ip-addresses-for-outbound-only"></a>アウトバウンド専用の静的 IP アドレス

NAT では、アウトバウンド接続をサブネットごとに定義できます。  同じ仮想ネットワーク内にある複数のサブネットに対し、それぞれ異なる NAT を割り当てることができます。 サブネットの構成は、使用する [NAT ゲートウェイ リソース](./nat-gateway-resource.md)を指定することで行います。 UDP と TCP のアウトバウンド フローには、それがどの仮想マシン インスタンスから送信されたものであれ、すべて NAT が使用されます。 

NAT は、Standard SKU の[パブリック IP アドレス リソース](./virtual-network-ip-addresses-overview-arm.md#standard)と[パブリック IP プレフィックス リソース](./public-ip-address-prefix.md)のどちらか、またはその両方を組み合わせたものと共に利用することができます。  パブリック IP プレフィックスを直接使用できるほか、複数の NAT ゲートウェイ リソースにプレフィックスのパブリック IP アドレスを割り振ることもできます。 すべてのトラフィックは、NAT によって、プレフィックスの IP アドレス範囲に調整されます。  デプロイの IP ホワイトリスト登録を容易に行うことが可能です。

サブネットのすべてのアウトバウンド トラフィックは NAT によって自動的に処理され、ユーザーによる構成は不要です。  ユーザー定義ルートは必要ありません。 NAT は他の[アウトバウンド シナリオ](../load-balancer/load-balancer-outbound-connections.md)よりも優先され、サブネットの既定のインターネット宛先に取って代わります。

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>スケーリングを目的とした、複数の IP アドレスによるオンデマンド SNAT

NAT は "ポート ネットワーク アドレス変換" (PNAT または PAT) を使用しており、ほとんどのワークロードに推奨されます。 アウトバウンド フローのオンデマンド割り当てを使用すれば、動的なワークロードや多様なワークロードも容易に受け入れることができます。 大がかりな事前計画や事前割り当ては不要であり、ひいてはアウトバウンド リソースのオーバープロビジョニングも避けることができます。 SNAT ポート リソースは共有され、特定の NAT ゲートウェイ リソースを使用してすべてサブネットから利用できます。また、必要に応じて提供されます。

NAT に接続された 1 つのパブリック IP アドレスで、UDP と TCP の最大 64,000 の同時フローに対応します。 最初は 1 つの IP アドレスで運用し、16 個のパブリック IP アドレスにまでスケールアップすることができます。

NAT を使用すると、仮想ネットワークからインターネットへのフローを作成することができます。 インターネットからの戻りトラフィックは、アクティブなフローへの応答でのみ許可されます。

ロード バランサーのアウトバウンド SNAT とは異なり、NAT には、アウトバウンド接続を行うことができる仮想マシン インスタンスのプライベート IP に関する制限がありません。  NAT であれば、セカンダリ IP 構成でアウトバウンド インターネット接続を作成できます。

## <a name="coexistence-of-inbound-and-outbound"></a>インバウンドとアウトバウンドの共存

NAT は、次の Standard SKU リソースと共に利用することができます。

- [Load Balancer](../load-balancer/load-balancer-overview.md)
- [パブリック IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [パブリック IP プレフィックス](../virtual-network/public-ip-address-prefix.md)

これらのリソースを NAT と組み合わせることで、サブネットへのインバウンド インターネット接続が可能となります。 サブネットからのアウトバウンド インターネット接続はすべて NAT から提供されます。

NAT および NAT と共存可能な Standard SKU 機能は、フローがどちら側から開始されたかを認識します。 インバウンドとアウトバウンドのシナリオを共存させることができます。 これらのシナリオでは、各機能によってフロー方向が認識されるので、ネットワーク アドレス変換が正しく実行されます。 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" width="512" title="Virtual Network NAT のフロー方向">
</p>

*図:Virtual Network NAT のフロー方向*

## <a name="fully-managed-highly-resilient"></a>フル マネージド、高い回復性

NAT は最初からスケールアウトに完全対応しています。 システムの増強 (スケールアウト) 操作は必要ありません。  NAT の操作は、Azure がお客様に代わって管理します。  NAT は常に複数の障害ドメインを有しているため、サービスの停止を招くことなく複数の障害に耐えることができます。

## <a name="tcp-reset-for-unrecognized-flows"></a>フローが認識されない場合の TCP リセット

存在しない TCP 接続での通信が試行されると、NAT のプライベート側が TCP リセット パケットを送信します。 たとえば、接続がアイドル タイムアウトに達した場合が該当します。 次に受信したパケットによって、プライベート IP アドレスには TCP リセットが返され、これによって接続の終了が伝えられて、接続は強制的に終了されます。

NAT のパブリック側からは、TCP リセット パケットなどのトラフィックは一切生成されません。  出力されるのは、お客様の仮想ネットワークによって生成されたトラフィックだけです。

## <a name="configurable-idle-timeout"></a>構成可能なアイドル タイムアウト

4 分というアイドル タイムアウトの既定値が使用され、最大 120 分にまで増やすことができます。 また、アイドル タイマーは、フロー上の任意のアクティビティ (TCP キープアライブを含む) でリセットすることもできます。

## <a name="regional-or-zone-isolation-with-availability-zones"></a>リージョン単位の分離と可用性ゾーンを使用したゾーン単位の分離

既定では、NAT はリージョン単位となっています。 [可用性ゾーン](../availability-zones/az-overview.md)のシナリオを作成する場合は、特定のゾーンに NAT を分離することができます (ゾーン デプロイ)。

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" width="512" title="Virtual Network NAT と可用性ゾーン">
</p>

*図:Virtual Network NAT と可用性ゾーン*

## <a name="multi-dimensional-metrics-for-observability"></a>監視のための多次元メトリック

ご自分の NAT の運用は、Azure Monitor で公開される多次元メトリックを通じて監視することができます。 これらのメトリックを使用して利用状況を監視したり、トラブルシューティングを行ったりすることができます。  NAT ゲートウェイ リソースでは、次のメトリックが公開されます。
- バイト
- パケット
- 破棄されたパケット
- 合計 SNAT 接続数
- 一定間隔ごとの SNAT 接続の状態遷移

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>SLA

一般提供時には、NAT のデータ パスの可用性が 99.9% 以上となります。

## <a name = "region-availability"></a>利用可能なリージョン

現在 NAT は、次のリージョンで提供されています。

- 西ヨーロッパ
- 東日本
- 米国東部 2
- 米国西部
- 米国西部 2
- 米国中西部

## <a name = "enable-preview"></a>パブリック プレビューへの参加

パブリック プレビューに参加するためには、サブスクリプションのご登録が必要です。  参加は 2 つの手順から成るプロセスとなっています。以下に、Azure CLI を使用した手順と Azure PowerShell を使用した手順を記載します。  アクティブ化が完了するまでに数分かかる場合があります。

### <a name="azure-cli"></a>Azure CLI

1. サブスクリプションをパブリック プレビューに登録します。

    ```azurecli-interactive
      az feature register --namespace Microsoft.Network --name AllowNatGateway
    ```

2. 登録をアクティブ化します。

    ```azurecli-interactive
      az provider register --namespace Microsoft.Network
    ```

### <a name="azure-powershell"></a>Azure PowerShell

1. サブスクリプションをパブリック プレビューに登録します。

    ```azurepowershell-interactive
      Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowNatGateway
    ```

2. 登録をアクティブ化します。

    ```azurepowershell-interactive
      Register-AzResourceProvider -ProviderNamespace Microsoft.Network
    ```

## <a name="pricing"></a>価格

NAT ゲートウェイは、2 つの測定値を使用して課金されます。

| 測定 | 料金 |
| --- | --- |
| リソース時間 | $0.045/時 |
| データ処理量 | $0.045/GB |

リソース時間は、NAT ゲートウェイ リソースが存在する期間に相当します。
データ処理量は、NAT ゲートウェイ リソースによって処理されたすべてのトラフィックに相当します。

パブリック プレビュー期間中は、価格が 50% 引きとなります。

## <a name="support"></a>サポート

NAT のサポートは、通常のサポート チャンネルを通じて提供されます。

## <a name="feedback"></a>フィードバック

サービスを改善するために、皆様のご意見をお待ちしております。 [パブリック プレビューに関するフィードバック](https://aka.ms/natfeedback)をお寄せください。  また、今後の新機能に関する提案や投票も、[NAT の UserVoice](https://aka.ms/natuservoice) で受け付けております。

## <a name="limitations"></a>制限事項

* NAT は、Standard SKU のパブリック IP、パブリック IP プレフィックス、ロード バランサーの各リソースと共に利用することができます。   Basic リソース (Basic Load Balancer など) やそれらから派生した製品を NAT と共存させることはできません。  Basic リソースは、NAT が構成されていないサブネットに配置する必要があります。
* サポートされるアドレス ファミリーは IPv4 です。  IPv6 アドレス ファミリーを NAT で扱うことはできません。  IPv6 プレフィックスを持つサブネットに NAT をデプロイすることはできません。
* NAT を使用している場合、NSG フロー ログはサポートされません。
* NAT を複数の仮想ネットワークにまたがって使用することはできません。

## <a name="next-steps"></a>次のステップ

* [NAT ゲートウェイ リソース](./nat-gateway-resource.md)について学習する。
* [UserVoice で Virtual Network NAT の新機能の構築を提案する](https://aka.ms/natuservoice)。
* [パブリック プレビューに関するフィードバックを送る](https://aka.ms/natfeedback)。
