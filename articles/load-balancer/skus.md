---
title: Azure Load Balancer の SKU
description: Azure Load Balancer の SKU の概要
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/21/2021
ms.author: allensu
ms.openlocfilehash: 0501f703ce32df37a755c05240b24b8262ccf314
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491167"
---
# <a name="azure-load-balancer-skus"></a>Azure Load Balancer の SKU

Azure Load Balancer には 2 つの SKU があります。

## <a name="sku-comparison"></a><a name="skus"></a> SKU の比較
Azure Load Balancer には、Basic、Standard、Gateway という 3 つの SKU があります。 各 SKU は特定のシナリオに対応しており、スケール、機能、価格の違いがあります。 

Basic と Standard の SKU の違いを比較して理解するには、次の表を参照してください。 詳しくは、「[Azure Standard Load Balancer の概要](./load-balancer-overview.md)」を参照してください。 現在プレビュー段階にあるサード パーティーのネットワーク仮想アプライアンス (NVA) に対応している Gateway SKU については、「[Gateway Load Balancer の概要](gateway-overview.md)」を参照してください

>[!NOTE]
> Microsoft では、Standard Load Balancer を推奨しています。
スタンドアロン VM、可用性セット、および仮想マシン スケール セットは、どちらか一方の SKU にのみ接続でき、両方には接続できません。 パブリック IP アドレスで使うときは、ロード バランサーとパブリック IP アドレスの SKU が一致していなければなりません。 Load Balancer とパブリック IP の SKU は変更できません。

| | Standard Load Balancer | Basic Load Balancer |
| --- | --- | --- |
| **シナリオ** |  ハイ パフォーマンスと非常に短い待機時間を必要とする場合に、ネットワーク層のトラフィックを負荷分散する機能を備えています。 リージョン内およびリージョン間でトラフィックをルーティングし、高い回復性を実現するために可用性ゾーンにルーティングします。 | 高可用性や冗長性を必要としない小規模なアプリケーション向けに搭載されています。 可用性ゾーンと互換性がありません。 |
| **バックエンドの種類** | IP ベース、NIC ベース | NIC ベース |
| **プロトコル** | TCP、UDP | TCP、UDP |
| **[フロントエンド IP 構成](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)** | 最大 600 の構成をサポートします。 | 最大 200 の構成をサポートします。 |
| **[バックエンド プールのサイズ](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)** | 最大 1,000 インスタンスをサポート。 | 最大 300 インスタンスをサポート。 |
| **バックエンド プール エンドポイント** | 単一の仮想ネットワーク内の任意の仮想マシンまたは仮想マシン スケール セット。 | 単一の可用性セットまたは仮想マシン スケール セット内の仮想マシン。 |
| **[正常性プローブ](./load-balancer-custom-probe-overview.md#types)** | TCP、HTTP、HTTPS | TCP、HTTP |
| **[正常性プローブ ダウン動作](./load-balancer-custom-probe-overview.md#probedown)** | インスタンス プローブがダウンし、__かつ__ すべてのプローブがダウンしても TCP 接続は存続。 | インスタンス プローブがダウンしても TCP 接続は存続。 すべてのプローブがダウンすると、すべての TCP 接続が終了。 |
| **可用性ゾーン** | 受信トラフィックと送信トラフィック用のゾーン冗長およびゾーン フロントエンド。 | 使用不可 |
| **診断** | [Azure Monitor 多次元メトリック](./load-balancer-standard-diagnostics.md) | サポートされていません |
| **HA ポート** | [内部ロード バランサーで使用可能](./load-balancer-ha-ports-overview.md) | 使用不可 |
| **既定でのセキュリティ保護** | ネットワーク セキュリティ グループで許可されている場合を除き、受信フローは禁止されます。 仮想ネットワークから内部ロードバランサーへの内部トラフィックは許可されています。 | 既定で開いています。 ネットワーク セキュリティ グループはオプションです。 |
| **送信規則** | [宣言型の送信 NAT 構成](./load-balancer-outbound-connections.md#outboundrules) | 使用不可 |
| **アイドルの TCP リセット** | [任意のルールで利用可能](./load-balancer-tcp-reset.md) | 使用不可 |
| **[複数のフロントエンド](./load-balancer-multivip-overview.md)** | 受信および[送信](./load-balancer-outbound-connections.md) | 受信のみ |
| **管理操作** | ほとんどの操作は 30 秒未満 | 一般に 60 ～ 90 秒以上 |
| **SLA** | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | 使用不可 | 
| **グローバル VNet ピアリング サポート** | Standard ILB は、グローバル VNet ピアリングを介してサポートされます | サポートされていません | 
| **[NAT Gateway サポート](https://docs.microsoft.com/azure/virtual-network/nat-gateway/nat-overview)** | Standard ILB と Standard Public LB の両方が Nat Gateway 経由でサポートされます。 | サポートされていません | 
| **[Private Link サポート](https://docs.microsoft.com/azure/private-link/private-link-overview)** | Standard ILB は Private Link 経由でサポートされます。 | サポートされていません | 
| **[リージョン間負荷分散 (プレビュー)](https://docs.microsoft.com/azure/load-balancer/cross-region-overview)** | Standard Public LB はリージョン間 LB 経由でサポートされます。 | サポートされていません | 

詳細については、「[Load Balancer の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)」を参照してください。 Standard Load Balancer について詳しくは、[概要](./load-balancer-overview.md)、[価格](https://aka.ms/lbpricing)、[SLA](https://aka.ms/lbsla) に関するページもご覧ください。

## <a name="limitations"></a>制限事項

- SKU[をLoad Balancerアップグレードできます](upgrade-basic-standard.md)。
- スタンドアロン仮想マシン リソース、可用性セット リソース、または仮想マシン スケール セット リソースは、1 つの SKU でのみ参照でき、両方では参照できません。
- [移動操作](../azure-resource-manager/management/move-resource-group-and-subscription.md):
  - リソース グループの移動操作 (同じサブスクリプション内) は、Standard Load Balancer および Standard パブリック IP で **サポートされています**。 
  - [サブスクリプション グループの移動操作](../azure-resource-manager/management/move-support-resources.md)は、Standard Load Balancer および Standard パブリック IP リソースではサポートされて **いません**。

## <a name="next-steps"></a>次のステップ

- Load Balancer の使用を開始するには、[パブリック Standard Load Balancer の作成](quickstart-load-balancer-standard-public-portal.md)に関するページを参照してください。
- [Standard Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)の使用について学習する。
- [正常性プローブ](load-balancer-custom-probe-overview.md)について学習する。
- [送信接続に対する Load Balancer](load-balancer-outbound-connections.md) の使用について学習する。
- [HA ポート負荷分散ルールでの Standard Load Balancer](load-balancer-ha-ports-overview.md) について学習する。
- [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)の詳細を確認する。
