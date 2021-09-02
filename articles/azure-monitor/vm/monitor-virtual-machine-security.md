---
title: 'Azure Monitor を使用して仮想マシンを監視する: セキュリティ'
description: 仮想マシンのセキュリティを監視するサービスと、仮想マシンと Azure Monitor の関連について説明します。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 8f32be602ac1ba8ea7bb5805ca8df120b0af917e
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674282"
---
# <a name="monitor-virtual-machines-with-azure-monitor-security-monitoring"></a>Azure Monitor を使用して仮想マシンを監視する: セキュリティの監視
この記事は、シナリオ「[Azure Monitor で仮想マシンとそのワークロードを監視する](monitor-virtual-machine.md)」の一部です。 仮想マシンのセキュリティを監視する Azure サービスと、仮想マシンと Azure Monitor の関連について説明しています。 Azure Monitor は、仮想マシンやその他のクラウド リソースの可用性とパフォーマンスを監視するように設計されています。 Azure Monitor に格納されている運用データはセキュリティ インシデントの調査に役立つことがありますが、Azure の他のサービスはセキュリティを監視するために設計されています。 

> [!IMPORTANT]
> セキュリティ サービスでは、Azure Monitor とは別に独自のコストが発生します。 これらのサービスを構成する前に、価格情報を参照して、それらの使用に対する適切な投資を判断してください。

## <a name="azure-services-for-security-monitoring"></a>セキュリティの監視のための Azure サービス
Azure Monitor では、アクティビティ ログ、メトリック、Log Analytics でサポートされるソース (Windows イベント (セキュリティ イベントを除く)、パフォーマンス カウンター、ログ、Syslog を含む) の運用データに重点が置かれています。 Azure でのセキュリティの監視は、Azure Security Center と Azure Sentinel によって実行されます。 これらのサービスではそれぞれ追加のコストが発生するため、実装する前にご使用の環境での価値を判断する必要があります。

[Azure Security Center](../../security-center/security-center-introduction.md) では、Azure リソースとハイブリッド サーバーに関する情報が収集されます。 Security Center では、セキュリティ イベントを収集することもできますが、インベントリ データ、評価のスキャンの結果、ポリシーの監査の収集に重点を置いて、脆弱性を強調し、是正措置を推奨します。 注目すべき機能には、対話型のネットワーク マップ、Just-In-Time の VM アクセス、アダプティブ ネットワークのセキュリティ強化機能、疑わしい実行可能ファイルをブロックする適応型アプリケーション制御が含まれます。

[Azure Defender for Servers](../../security-center/azure-defender.md) は、Security Center によって提供されるサーバー評価ソリューションです。 Defender for Servers では、Windows セキュリティ イベントを Log Analytics に送信できます。 Security Center は、アラートまたは分析に関して Windows のセキュリティ イベントに依存していません。 この機能を使用すると、調査などの目的でイベントを一元的にアーカイブできます。

[Azure Sentinel](../../sentinel/overview.md) は、セキュリティ情報イベント管理 (SIEM) およびセキュリティ オーケストレーション自動応答 (SOAR) ソリューションです。 Sentinel では、Microsoft およびサードパーティのさまざまなソースからセキュリティ データを収集して、アラート、視覚化、自動化を提供します。 このソリューションでは、可能な限り多くのセキュリティ ログ (Windows セキュリティ イベントを含む) を統合することに重点が置かれています。 Azure Sentinel では、Windows セキュリティ イベントのログを収集することもでき、通常、Log Analytics ワークスペースを Security Center と共有しています。 セキュリティ イベントは、同じワークスペースを共有している場合、Azure Sentinel または Security Center からのみ収集できます。 Security Center とは異なり、セキュリティ イベントは、Azure Sentinel でのアラートと分析の重要なコンポーネントです。

[Defender for Endpoint](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint) は、企業ネットワークによる高度な脅威に対する防御、検出、調査、対応を支援するように設計されたエンタープライズ エンドポイント セキュリティ プラットフォームです。 これは、Windows ユーザー デバイスの保護に主に重点を置いて設計されています。 Defender for Endpoint では、セキュリティの問題と脆弱性について、さまざまなオペレーティング システムのワークステーション、サーバー、タブレット、携帯電話を監視します。 Defender for Endpoint では、Microsoft エンドポイント マネージャーと密接に連携し、データを収集してセキュリティ評価が提供されます。 データ収集は主に ETW トレース ログに基づいており、分離されたワークスペースに格納されます。

## <a name="integration-with-azure-monitor"></a>Azure Monitor との統合
次の表に、Azure Monitor とセキュリティ サービスの統合ポイントを示します。 すべてのサービスで同じ Log Analytics エージェントが使用され、仮想マシンに他のコンポーネントがデプロイされないので、複雑さが軽減されます。 Security Center と Azure Sentinel では、ログ クエリを使用して、さまざまなサービスによって収集されたデータを関連付けることができるように、Log Analytics ワークスペースにデータが格納されます。 または、セキュリティ データ、可用性データ、パフォーマンス データを 1 つのビューに組み合わせたカスタム ブックを作成することができます。

| 統合ポイント       | Azure Monitor | Azure Security Center | Azure Sentinel | Defender for Endpoint |
|:---|:---|:---|:---|:---|
| セキュリティ イベントの収集     |   | X | X | X |
| Log Analytics ワークスペースへのデータの格納 | X | X | X |   | 
| Log Analytics エージェントの使用     | X | X | X | X | 


## <a name="workspace-design-considerations"></a>ワークスペースの設計に関する考慮事項
「[Azure Monitor を使用した仮想マシンの監視: 監視の構成](monitor-virtual-machine-configure.md#create-and-prepare-a-log-analytics-workspace)」で説明されているように、AzureMonitor とセキュリティ サービスには Log Analytics ワークスペースが必要です。 特定の要件に応じて、共通のワークスペースを共有するか、可用性とパフォーマンスのデータをセキュリティ データから分離することができます。 ワークスペースの構成を設計するために検討する必要があるロジックの詳細については、「[Azure Monitor ログのデプロイの設計](../logs/design-logs-deployment.md)」を参照してください。

## <a name="agent-deployment"></a>エージェントの展開
Security Center は、Log Analytics エージェントを Azure 仮想マシンに自動的にデプロイするように構成できます。 これは、同じエージェントをデプロイする Azure Monitor と冗長になるように見えますが、それぞれで独自の構成が実行されるので、両方を有効にすることになるでしょう。 たとえば、既に Azure Monitor によって監視されているマシンを Security Center でプロビジョニングしようとすると、既にインストールされているエージェントが使用され、Security Center ワークスペースの構成が追加されます。

## <a name="next-steps"></a>次のステップ

* [仮想マシン用に収集された監視データを分析する](monitor-virtual-machine-analyze.md)
* [収集されたデータからアラートを作成する](monitor-virtual-machine-alerts.md)
