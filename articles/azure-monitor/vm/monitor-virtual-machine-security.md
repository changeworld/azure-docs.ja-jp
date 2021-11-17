---
title: 'Azure Monitor を使用して仮想マシンを監視する: セキュリティ'
description: 仮想マシンのセキュリティを監視するサービスと、仮想マシンと Azure Monitor の関連について説明します。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: c34a860f7b949a387af8c4905a948e1f47f68057
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310302"
---
# <a name="monitor-virtual-machines-with-azure-monitor-security-monitoring"></a>Azure Monitor を使用して仮想マシンを監視する: セキュリティの監視
この記事は、シナリオ「[Azure Monitor で仮想マシンとそのワークロードを監視する](monitor-virtual-machine.md)」の一部です。 仮想マシンのセキュリティを監視する Azure サービスと、仮想マシンと Azure Monitor の関連について説明しています。 Azure Monitor は、仮想マシンやその他のクラウド リソースの可用性とパフォーマンスを監視するように設計されています。 Azure Monitor に格納されている運用データはセキュリティ インシデントの調査に役立つことがありますが、Azure の他のサービスはセキュリティを監視するために設計されています。 

> [!IMPORTANT]
> セキュリティ サービスでは、Azure Monitor とは別に独自のコストが発生します。 これらのサービスを構成する前に、価格情報を参照して、それらの使用に対する適切な投資を判断してください。

## <a name="azure-services-for-security-monitoring"></a>セキュリティの監視のための Azure サービス
Azure Monitor では、アクティビティ ログ、メトリック、Log Analytics でサポートされるソース (Windows イベント (セキュリティ イベントを除く)、パフォーマンス カウンター、ログ、Syslog を含む) の運用データに重点が置かれています。 Azure のセキュリティは Microsoft Defender for Cloud と Microsoft Sentinel によって監視されます。 これらのサービスではそれぞれ追加のコストが発生するため、実装する前にご使用の環境での価値を判断する必要があります。


## <a name="integration-with-azure-monitor"></a>Azure Monitor との統合
次の表に、Azure Monitor とセキュリティ サービスの統合ポイントを示します。 すべてのサービスで同じ Log Analytics エージェントが使用され、仮想マシンに他のコンポーネントがデプロイされないので、複雑さが軽減されます。 Defender for Cloud と Microsoft Sentinel では、ログ クエリを使用して、さまざまなサービスによって収集されたデータを関連付けることができるように、Log Analytics ワークスペースにデータが格納されます。 または、セキュリティ データ、可用性データ、パフォーマンス データを 1 つのビューに組み合わせたカスタム ブックを作成することができます。

| 統合ポイント       | Azure Monitor | Microsoft Defender for Cloud | Microsoft Sentinel | Defender for Endpoint |
|:---|:---|:---|:---|:---|
| セキュリティ イベントの収集     |   | X | X | X |
| Log Analytics ワークスペースへのデータの格納 | X | X | X |   | 
| Log Analytics エージェントの使用     | X | X | X | X | 



## <a name="agent-deployment"></a>エージェントの展開
Defender for Cloud は、Log Analytics エージェントを Azure 仮想マシンに自動的にデプロイするように構成できます。 これは、同じエージェントをデプロイする Azure Monitor と冗長になるように見えますが、それぞれで独自の構成が実行されるので、両方を有効にすることになるでしょう。 たとえば、既に Azure Monitor によって監視されているマシンを Defender for Cloud でプロビジョニングしようとすると、既にインストールされているエージェントが使用され、Defender for Cloud ワークスペースの構成が追加されます。

## <a name="next-steps"></a>次のステップ

* [仮想マシン用に収集された監視データを分析する](monitor-virtual-machine-analyze.md)
* [収集されたデータからアラートを作成する](monitor-virtual-machine-alerts.md)
