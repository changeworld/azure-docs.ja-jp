---
title: Azure Monitor for VMs (プレビュー) とは? | Microsoft Docs
description: アプリケーション コンポーネントとその依存関係を自動的に検出およびマッピングするだけでなく、AzureVM の正常性とパフォーマンスを監視する機能を持つ Azure Monitor for VMs の概要。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/14/2019
ms.openlocfilehash: e9a3df0172f276800a3049675f6e858db7f0370c
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307293"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Azure Monitor for VMs (プレビュー) とは?

Azure Monitor for VMs では、ご利用の Azure 仮想マシン (VM) および仮想マシン スケール セットの大規模な監視が行われます。 これにより、ご利用の Windows VM および Linux VM のプロセスや、その他のリソースおよび外部プロセスとの依存関係を監視することにより、それらの VM のパフォーマンスおよび正常性が分析されます。 

この場合は、オンプレミスまたは別のクラウド プロバイダーでホストされている VM について、パフォーマンスおよびアプリケーションの依存関係の監視もサポートされています。 次の主な機能によって、詳細な分析情報が提供されます。

- **事前に定義された、傾向を示すパフォーマンス グラフ**:ゲスト VM オペレーティング システムから主要なパフォーマンス メトリックが表示されます。

- **依存関係マップ**: さまざまなリソース グループおよびサブスクリプションから、該当する VM と相互接続されているコンポーネントが表示されます。  

>[!NOTE]
>最近、当社のパブリック プレビューのお客様から受け取ったフィードバックに基づいて正常性機能に対して行っている[変更を発表](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
)しました。 実施する変更の数を考慮して、新しいお客様への正常性機能の提供を停止する予定です。 既存のお客様は、引き続き正常性機能を使用できます。 詳細については、[一般提供の FAQ](vminsights-ga-release-faq.md) に関するページを参照してください。  

Azure Monitor ログとの統合により、強力な集計とフィルター処理が実現されています。さらに、時間の経過に伴うデータの傾向を分析することができます。 そうした包括的なワークロード監視は、Azure Monitor または Service Map 単独では実現できません。  

1 つの VM 内のこのデータをその仮想マシンから直接表示するか、または Azure Monitor を使用して複数の VM の集計ビューを提供できます。ここで、そのビューは Azure リソース コンテキストまたはワークスペース コンテキスト モードをサポートします。 詳細については、[アクセス モードの概要](../platform/design-logs-deployment.md#access-mode)に関するページを参照してください。

![Azure portal での仮想マシン分析情報のパースペクティブ](./media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor for VMs を使用すると、重要なアプリケーションのパフォーマンスおよび可用性を予測することができます。 これは、パフォーマンスのボトルネックやネットワークに関する問題を識別します。 Azure Monitor for VMs はまた、問題が他の依存関係に関連しているかどうかの把握にも役立ちます。  

## <a name="data-usage"></a>データ利用状況

Azure Monitor for VM をデプロイすると、ご利用の VM によって収集されたデータが取り込まれ、Azure Monitor に格納されます。 収集されたパフォーマンスおよび依存関係データは、Log Analytics ワークスペースに格納されます。 「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」ページに公開されている価格に基づいて、Azure Monitor for VMs は以下に対して課金されます。

- 取り込まれ格納されるデータ。
- 作成されるアラート ルール。
- 送信される通知。 

ログのサイズは、パフォーマンス カウンターの文字列の長さによって異なり、仮想マシンに割り当てられた論理ディスクおよびネットワーク アダプターの数に応じて大きくなる可能性があります。 ワークスペースを既にお持ちで、これらのカウンターを収集している場合、重複する課金は適用されません。 Service Map を既に使用している場合、確認できる変更点は、Azure Monitor に送信される追加の接続データだけです。

## <a name="next-steps"></a>次の手順

ご利用の仮想マシンを監視する上で役立つ要件と方法については、[Azure Monitor for VMs のデプロイ](vminsights-enable-overview.md)に関するページをご覧ください。
