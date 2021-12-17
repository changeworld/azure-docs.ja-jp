---
title: Azure Monitor を使用して仮想マシンを監視する
description: Azure Monitor を使用して、仮想マシンとそのワークロードの正常性とパフォーマンスを監視する方法について説明します。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/02/2021
ms.openlocfilehash: c1506fe5fae652c198d53b5b354e818ede5c8a46
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315788"
---
# <a name="monitor-virtual-machines-with-azure-monitor"></a>Azure Monitor を使用して仮想マシンを監視する
このシナリオでは、Azure Monitor を使用して、仮想マシンとそのワークロードの正常性とパフォーマンスを監視する方法について説明します。 これには、傾向を識別するために収集されたデータの監視、分析、視覚化に不可欠なテレメトリのコレクションと、重大な問題が予防的に通知されるアラートを構成する方法が含まれます。

この記事ではシナリオを紹介し、Azure Monitor で仮想マシンを監視するための一般的な概念について説明します。 特定の分野を参照する場合は、次の表で説明するこのシナリオの一部である他のいずれかの記事を参照してください。

| [アーティクル] | 説明 |
|:---|:---|
| [監視を有効にする](monitor-virtual-machine-configure.md) | VM Insights を有効にし、各仮想マシンで監視を有効にするなど、仮想マシンを監視するように Azure Monitor を構成します。  |
| [解析](monitor-virtual-machine-analyze.md) | 仮想マシンとそのゲスト オペレーティングおよびアプリケーションから Azure Monitor によって収集された監視データを分析し、傾向と重要な情報を特定します。 |
| [警告](monitor-virtual-machine-alerts.md)   | アラートを作成して、監視データの重大な問題を事前に特定します。 |
| [セキュリティを監視する](monitor-virtual-machine-security.md) | 仮想マシンのセキュリティを監視するための Azure サービスについて説明します。 |
| [ワークロードの監視](monitor-virtual-machine-workloads.md) | 仮想マシンで実行されているアプリケーションや他のワークロードを監視します。 |

> [!IMPORTANT]
> このシナリオには、一般提供されていない機能は含められていません。 [仮想マシンのゲストの正常性](vminsights-health-overview.md)などのパブリック プレビューの機能によって、ここに示す推奨事項が大幅に変更される可能性があります。 プレビュー機能が一般提供に移行すると、シナリオが更新されます。

## <a name="types-of-machines"></a>マシンの種類
このシナリオには、Azure Monitor を使用した次の種類のマシンの監視が含まれています。 ここで説明するプロセスの多くは、マシンの種類に関係なく同じです。 さまざまな種類のマシンに関する考慮事項が必要に応じて明確に特定されています。 マシンの種類は次のとおりです。 

- Azure 仮想マシン。
- Azure 仮想マシン スケール セット。
- ハイブリッド マシン。これは、他のクラウド内、マネージド サービス プロバイダーを使用して、またはオンプレミスで実行されている仮想マシンです。 また、オンプレミスで実行されている物理マシンも含まれます。

## <a name="layers-of-monitoring"></a>監視のレイヤー
監視を必要とする仮想マシンには、基本的に 4 つのレイヤーがあります。 各レイヤーには、テレメトリと監視の要件の個別のセットがあります。 

| レイヤー | 説明 |
|:---|:---|
| 仮想マシンのホスト | Azure のホスト仮想マシン。 Azure Monitor は、他のクラウドのホストにアクセスできませんが、ゲスト オペレーティング システムから収集された情報に依存する必要があります。 ホストは、構成の変更などのアクティビティを追跡する場合に役立ちますが、通常は重大なアラートには使用されません。 |
| ゲスト オペレーティング システム | 仮想マシンで実行されているオペレーティング システム。これは Windows または Linux の何らかのバージョンです。 パフォーマンス データやイベントなど、ゲスト オペレーティング システムから大量の監視データを使用できます。 Azure Monitor の VM Insights によって、ゲスト オペレーティング システムの正常性とパフォーマンスを監視するための大量のロジックが提供されます。 |
| ワークロード | ビジネス アプリケーションをサポートするゲスト オペレーティング システムで実行されているワークロード。 Azure Monitor では、一部のワークロードに対する定義済みの監視が提供されています。 通常は、生成される監視データを使用して、他のワークロードのデータ収集とアラートを構成する必要があります。 |
| Application | 仮想マシンに依存するビジネス アプリケーションは、[Application Insights](../app/app-insights-overview.md) を使用して監視できます。 

:::image type="content" source="media/monitor-virtual-machines/monitoring-layers.png" alt-text="監視レイヤーを示す図。" lightbox="media/monitor-virtual-machines/monitoring-layers.png":::

## <a name="vm-insights"></a>VM insights
このシナリオでは、仮想マシンを監視するための Azure Monitor の主要機能である [VM Insights](../vm/vminsights-overview.md) に焦点を合わせます。 VM Insights では次の機能が提供されます。

- 仮想マシンのゲスト オペレーティング システムとワークロードの監視を有効にする、エージェントのオンボードの簡略化。 
- 仮想マシンのゲスト オペレーティング システムからコア パフォーマンス メトリックを分析するために使用できる、事前に定義された傾向を示すパフォーマンス グラフとブック。
- 各仮想マシンで実行されているプロセスと、他のマシンや外部ソースと相互接続されたコンポーネントを表示する依存関係マップ。

## <a name="agents"></a>エージェント
Azure Monitor などのあらゆる監視ツールでは、ゲスト オペレーティング システムからデータを収集するために、マシンにエージェントがインストールされている必要があります。 Azure Monitor には現在、異なるデータを収集し、異なる場所にデータを送信し、さまざまな機能をサポートする複数のエージェントがあります。 VM Insights では、ほとんどのお客様が使用するエージェントのデプロイと構成が管理されます。 次の表では、さまざまなエージェントによってサポートされている特定のシナリオが必要な場合に備えて、これらのエージェントについて説明します。 さまざまなエージェントの詳細な説明と比較については、「[Azure Monitor エージェントの概要](../agents/agents-overview.md)」をご覧ください。

> [!NOTE]
> Azure Monitor エージェントは、必要な機能を得た後、Log Analytics エージェント、診断拡張機能、Telegraf エージェントの完全な後継となります。 それらのその他のエージェントは引き続き、VM Insights、Microsoft Defender for Cloud、Microsoft Sentinel などの機能に必要です。

- [Azure Monitor エージェント](../agents/agents-overview.md#azure-monitor-agent): Azure、他のクラウド環境、オンプレミスの仮想マシンをサポートします。 Azure Monitor のメトリックとログにデータを送信します。 VM Insights、Azure Security Center、Azure Sentinel に完全対応となったとき、Log Analytics エージェントと診断拡張機能に完全に取って代わります。
- [Log Analytics エージェント](../agents/agents-overview.md#log-analytics-agent): Azure、他のクラウド環境、オンプレミスの仮想マシンをサポートします。 Azure Monitor ログにデータを送信します。 VM Insights および監視ソリューションをサポートしています。 このエージェントは System Center Operations Manager に使用されているのと同じエージェントです。
- [Dependency Agent](../agents/agents-overview.md#dependency-agent): 仮想マシンで実行されているプロセスとその依存関係に関するデータを収集します。 Azure にデータを送信するために Log Analytics エージェントに依存しており、 VM Insights、Service Map、Wire Data 2.0 の各ソリューションがサポートされています。
- [Azure 診断拡張機能](../agents/agents-overview.md#azure-diagnostics-extension): Azure Monitor 仮想マシンでのみ使用できます。 Azure Event Hubs および Azure Storage にデータを送信します。

## <a name="next-steps"></a>次の手順

[仮想マシン用に収集された監視データを分析する](monitor-virtual-machine-analyze.md)
