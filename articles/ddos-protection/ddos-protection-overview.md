---
title: Azure DDoS Protection Standard の概要
description: Azure DDoS Protection Standard をアプリケーション設計のベスト プラクティスと組み合わせることにより、DDoS 攻撃に対する防御を提供する方法について説明します。
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: c3931cafb6c755c6119bf8deb4ecc6b58ea7bc51
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938445"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS Protection Standard の概要

分散型サービス拒否 (DDoS) 攻撃は、アプリケーションをクラウドに移行している顧客が直面する可用性とセキュリティに関する最大の関心事の一部です。 DDoS 攻撃では、アプリケーションのリソースを使い果たし、正当なユーザーがアプリケーションを使用できなくなるようにすることが試みられます。 DDoS 攻撃は、インターネット経由で一般に到達可能なすべてのエンドポイントで実行できます。

Azure のすべてのプロパティは、追加コストなしで Azure のインフラストラクチャ DDoS (Basic) Protection によって保護されています。 グローバルにデプロイされる Azure ネットワークのスケールと容量が、常時有効なトラフィック監視とリアルタイムのリスク軽減によって、一般的なネットワーク層攻撃からの保護を提供します。 DDoS Protection Basic には、ユーザーの構成またはアプリケーションの変更は不要です。 DDoS Protection Basic では、Azure DNS などの PaaS サービスを含むすべての Azure サービスが保護されます。

Azure DDoS Protection Standard は、アプリケーションの設計に関するベスト プラクティスと組み合わせることにより、DDoS 攻撃から保護するための強化された DDoS 軽減機能が提供されます。 この機能は、仮想ネットワーク内にあるお客様固有の Azure リソースを保護するために、自動的に調整されます。 保護は新規または既存の仮想ネットワークで簡単に有効にでき、アプリケーションやリソースの変更は必要ありません。 ログ、アラート、テレメトリなど、基本サービスにはない利点がいくつかあります。 

![Azure DDoS Protection サービスの比較](./media/ddos-protection-overview/ddos-comparison.png)

Azure DDoS Protection では、顧客データは保存されません。

## <a name="features"></a>特徴

- **ネイティブのプラットフォーム統合:** Azure にネイティブに統合します。 Azure Portal による構成が含まれます。 DDoS Protection Standard は、ユーザーのリソースおよびリソース構成を理解しています。
- **ターンキー保護:** DDoS Protection Standard が有効になるとすぐに、簡略化された構成によって、仮想ネットワーク上のすべてのリソースが直ちに保護されます。 ユーザーが介入したり、ユーザーが定義したりする必要はありません。 DDoS Protection Standard では、攻撃が検出されるとすぐに自動で軽減されます。
- **常時接続のトラフィック監視:** DDoS 攻撃の兆候を検出するために、アプリケーションのトラフィック パターンが 24 時間 365 日監視されます。 軽減策は、保護ポリシーを超えた場合に実行されます。
- **アダプティブ チューニング:** インテリジェント トラフィック プロファイリングにより、一定期間にわたってアプリケーションのトラフィックが学習され、そのサービスに最も適したプロファイルが選択および更新されます。 このプロファイルは、時間の経過とともにトラフィックが変化すると調整されます。
- **多層保護:** Web アプリケーション ファイアウォールと共に使用する場合、フルスタックの DDoS 保護が提供され、ネットワーク層 (Azure DDoS Protection Standard によって提供されるレイヤー 3 と 4) とアプリケーション層 (WAF によって提供されるレイヤー 7) の両方で保護が得られます。 WAF オファリングには、Azure [Application Gateway WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) に加えて、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall) で利用できるサードパーティの Web アプリケーション ファイアウォール オファリングが含まれています。
- **広範囲にわたる軽減スケール:** 60 種類を超える攻撃を軽減することができ、地球規模の容量を利用して、過去最大の DDoS 攻撃からも保護されます。
- **攻撃の分析:** 攻撃中の 5 分ごとの詳細なレポートと、攻撃終了後の完全な概要を取得します。 攻撃中のほぼリアルタイムの監視のために、軽減フローのログが [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md) に、またはオフラインのセキュリティ情報イベント管理 (SIEM) システムにストリーム配信されます。
- **攻撃メトリック:** 各攻撃から要約されたメトリックに Azure Monitor 経由でアクセスできます。
- **攻撃アラート:** 組み込みの攻撃メトリックを使用して、攻撃の開始時と停止時、およびその攻撃の期間にわたってアラートを構成できます。 アラートは、Microsoft Azure Monitor ログ、Splunk、Azure Storage、電子メール、Azure portal などの運用ソフトウェアに統合されます。
- **DDoS Rapid Response**:攻撃の調査と分析の支援を受けるため、DDoS Protection Rapid Response (DRR) チームを関与させます。 詳細については、[DDoS Rapid Response](ddos-rapid-response.md) に関するページを参照してください。
- **コストの保証:** データ転送とアプリケーションは、ドキュメント化された DDoS 攻撃のサービス クレジットをスケールアウトします。

## <a name="pricing"></a>価格

Azure DDoS Protection Standard の価格については、「[Azure DDoS Protection の価格](https://azure.microsoft.com/pricing/details/ddos-protection/)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [DDoS Protection プランを作成する](manage-ddos-protection.md)