---
title: Azure Monitor によって収集される監視データ | Microsoft Docs
description: Azure Monitor によって収集される監視データは、軽量でほぼリアルタイムのシナリオをサポートできるメトリックと、高度な分析に使用されるログに分けられます。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: bwren
ms.openlocfilehash: 84ab63d145d9726fad83b7b2337542fef5c8743d
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718964"
---
# <a name="monitoring-data-collected-by-azure-monitor"></a>Azure Monitor によって収集される監視データ
[Azure Monitor](../overview.md) は、アプリケーションと、それらが依存するリソースを監視するのに役立つサービスです。 この機能の中心にあるのは、監視対象のリソースからのテレメトリとその他のデータのストレージです。 この記事では、このデータが Azure Monitor によってどのように格納され、使用されるかを詳細に説明します。

Azure Monitor によって収集されるすべてのデータは、2 つの基本的な種類である[メトリック](#metrics)と[ログ](#logs)のいずれかに合致します。 メトリックは、特定の時点におけるシステムの何らかの側面を表す数値です。 メトリックは軽量であり、リアルタイムに近いシナリオをサポートできます。 ログには、種類ごとに異なるプロパティ セットを持つレコードに編成されたさまざまな種類のデータが含まれます。 イベントやトレースなどの利用統計情報は、組み合わせて分析できるように、パフォーマンス データとともにログとして格納されます。

![Azure Monitor の概要](media/data-collection/overview.png)

## <a name="metrics"></a>メトリック
メトリックは、特定の時点におけるシステムの何らかの側面を表す数値です。 メトリックは軽量であり、リアルタイムに近いシナリオをサポートできます。 メトリックは、値の変化とは無関係に、一定の間隔で収集されます。 これらは頻繁にサンプリングでき、比較的単純なロジックですばやく起動できるため、アラートを発行するときに役に立ちます。 

たとえば、プロセッサ使用率を仮想マシンから 毎分収集したり、アプリケーションにログインしたユーザーの数を 10 分間隔で収集したりします。 そのように収集した値の 1 つ、または 2 つの値の差が、定義したしきい値を超えたときに、アラートを発生させることができます。

Azure のメトリックの具体的な属性には、以下が含まれます。

* メトリックの定義で特に指定がない限り、1 分間隔で収集されます。
* メトリック名と、カテゴリとして機能する名前空間によって一意に識別されます。
* 93 日間格納されます。 長期的な傾向を見るため、ログにメトリックをコピーできます。

各メトリック値には、以下のプロパティがあります。
* 値が収集された時刻。
* 値が表す測定の種類。
* 値が関連付けられているリソース。
* 値そのもの。
* 一部のメトリックは、次のセクションで説明されている複数のディメンションを持つ場合があります。 カスタム メトリックは最大 10 個のディメンションを持つことができます。

### <a name="multi-dimensional-metrics"></a>多次元メトリック
メトリックのディメンションは、メトリックの値を記述するための追加データを保持する名前と値のペアです。 たとえば、_使用可能なディスク領域_メトリックは、_C:_、_D:_ という値がある_ドライブ_と呼ばれるディメンションを持つことができ、これらの値を使用して、すべてのドライブまたは個別のドライブで使用可能なディスク領域を表示できます。 

次の例は、_ネットワーク スループット_という名前の架空のメトリック用の 2 つのデータセットを示しています。 最初のデータセットにはディメンションがありません。 2 番目のデータセットは、I_P Address_ と_方向_という 2 つのディメンションの値を示しています。

### <a name="network-throughput"></a>ネットワーク スループット

 |Timestamp        | メトリック値 | 
   | ------------- |:-------------| 
   | 8/9/2017 8:14 | 1,331.8 Kbps | 
   | 8/9/2017 8:15 | 1,141.4 Kbps |
   | 8/9/2017 8:16 | 1,110.2 Kbps |

このディメンションのないメトリックは、「特定の時点でのネットワークのスループットは?」のような基本的な質問にのみ答えることができます。

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>ネットワーク スループット + 2 つのディメンション ("IP" と "方向")

| Timestamp          | ディメンション "IP" | ディメンション "方向" | メトリック値| 
   | ------------- |:-----------------|:------------------- |:-----------|  
   | 8/9/2017 8:14 | IP="192.168.5.2" | 方向 = "送信"    | 646.5 Kbps |
   | 8/9/2017 8:14 | IP="192.168.5.2" | 方向 = "受信" | 420.1 Kbps |
   | 8/9/2017 8:14 | IP="10.24.2.15"  | 方向 = "送信"    | 150.0 Kbps | 
   | 8/9/2017 8:14 | IP="10.24.2.15"  | 方向 = "受信" | 115.2 Kbps |
   | 8/9/2017 8:15 | IP="192.168.5.2" | 方向 = "送信"    | 515.2 Kbps |
   | 8/9/2017 8:15 | IP="192.168.5.2" | 方向 = "受信" | 371.1 Kbps |
   | 8/9/2017 8:15 | IP="10.24.2.15"  | 方向 = "送信"    | 155.0 Kbps |
   | 8/9/2017 8:15 | IP="10.24.2.15"  | 方向 = "受信" | 100.1 Kbps |

このメトリックは、「各 IP アドレスのネットワーク スループットは?」 や 「データの送信量と受信量?」といった質問に応えることができます。 多次元メトリックは、ディメンションを持たないメトリックに比べ、分析と診断に使用できる多数の値を提供します。

### <a name="value-of-metrics"></a>メトリックの値
通常は、個々のメトリックが単独で提供する分析情報はほとんどありません。 それらが提供するのは、単純なしきい値との比較以外のコンテキストを持たない単一の値です。 それらは、他のメトリックと組み合わせてパターンや傾向を識別したり、特定の値に関するコンテキストを提供するログと組み合わせたりするときに役に立ちます。 

たとえば、アプリケーションの特定の時点での特定のユーザー数からアプリケーションの正常性に関する情報を得られることはほとんどありません。 ただし、同じメトリックの複数の値によって示されるユーザー数の突然の低下は、問題を示唆している場合があります。 アプリケーションによってスローされ、異なるメトリックによって示される過剰な例外によって、低下を引き起こしているアプリケーションの問題を識別できることがあります。 コンポーネントでのエラーを識別するためにアプリケーションが作成するイベントは、根本原因を特定するのに役立ちます。

### <a name="sources-of-metric-data"></a>メトリック データのソース
Azure Monitor によって収集されるメトリックのソースには、基本的なものが 3 つあります。 これらのメトリックはすべて、ソースとは無関係に、一緒に評価可能な場所であるメトリック ストアで使用できます。

**プラットフォームのメトリック**は Azure リソースによって作成され、リソースの正常性とパフォーマンスについての可視化を提供します。 リソースの種類ごとに[別個のメトリックのセット](../../azure-monitor/platform/metrics-supported.md)が作成され、必要な構成はありません。 

**アプリケーション メトリック**は、監視対象のアプリケーションについて Application Insights によって作成され、パフォーマンスの問題を検出し、アプリケーションがどのように使用されているかの傾向を追跡する助けになります。 これには、_サーバー応答時間_と_ブラウザー例外_などの値が含まれます。

**カスタム メトリック**は、自動的に利用できる標準メトリックに加えて、ユーザーが定義するメトリックです。 カスタム メトリックは、そのリソースと同じリージョン内の 1 つのリソースに対して作成する必要があります。 カスタム メトリックの作成には、次の方法を使用できます。
    - Application Insights によって監視されている[アプリケーション内にカスタム メトリックを定義](../../azure-monitor/app/api-custom-events-metrics.md)します。 これらは標準セットのアプリケーション メトリックに追加されるものです。
    - [Windows Diagnostic Extension (WAD)](../../azure-monitor/platform/diagnostics-extension-overview.md) を使用して Windows 仮想マシンからカスタム メトリックを発行します。
    - [InfluxData Telegraf エージェント](https://www.influxdata.com/time-series-platform/telegraf/)を使用して Linux 仮想マシンからカスタム メトリックを発行します。
    - カスタム メトリックの API を使用して Azure のサービスからカスタム メトリックを記述します。
    
![メトリックの概要](media/data-collection/metrics-overview.png)

### <a name="what-can-you-do-with-metrics"></a>メトリックで行えること
メトリックを使用して実行できるタスクには、以下が含まれます。

- [メトリックス エクスプローラー](../../azure-monitor/platform/metrics-charts.md)を使用して、収集されたメトリックを分析し、それらをグラフにプロットします。 グラフを [Azure ダッシュボード](../../azure-portal/azure-portal-dashboards.md)にピン留めすることで、VM、Web サイト、ロジック アプリなどのリソースのパフォーマンスを追跡します。
- メトリックがしきい値を超えたときに、通知を送信するか[自動化されたアクション](action-groups.md)を実行する[メトリック アラート ルール](alerts-metric.md)を構成します。
- [自動スケーリング](../../azure-monitor/platform/autoscale-overview.md)を使用して、しきい値を超えるメトリックに基づいてリソースを増加または減少させます。
- メトリックを Log Analytics にルーティングし、メトリック データをログ データと一緒に分析して、93 日よりも長くメトリック値を格納します。 
- メトリックを[イベント ハブ](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md)にストリーム配信して、それらを [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) または外部システムにルーティングします。
- コンプライアンス、監査、オフライン レポートの目的で、リソースのパフォーマンスや正常性の履歴を[アーカイブ](../../azure-monitor/learn/tutorial-archive-data.md)します。
- コマンドラインまたはカスタム アプリケーションから、[PowerShell コマンドレット](https://docs.microsoft.com/powershell/module/azurerm.insights/?view=azurermps-6.7.0)または [REST API](../../azure-monitor/platform/rest-api-walkthrough.md) を使用して、メトリック値にアクセスします。



### <a name="viewing-metrics"></a>メトリックの表示
Azure Monitor のメトリックは、迅速な取得のために最適化された時系列のデータベースに格納されます。メトリックの値は 93 日間にわたって格納されます。 長期的な分析やトレンド分析のためには、ログにメトリックをコピーします。

メトリック データは、前述のように、さまざまな方法で使用されます。 [メトリックス エクスプ ローラー](../../azure-monitor/platform/metrics-charts.md)を使用して、メトリック ストア内のデータを直接分析し、一定期間にわたる複数のメトリックの値をグラフにします。 グラフは、対話形式で表示したり、ダッシュボードにピン留めして他の視覚化と一緒に表示したりできます。 [Azure monitoring REST API](../../azure-monitor/platform/rest-api-walkthrough.md) を使用してメトリックを取得することもできます。

![メトリックス エクスプローラー](media/data-collection/metrics-explorer.png)





## <a name="logs"></a>ログ
ログには、種類ごとに異なるプロパティ セットを持つレコードに編成されたさまざまな種類のデータが含まれます。 ログは、メトリックスのような数値を含むことができますが、通常は、詳細な説明付きのテキスト データが含まれます。 さらに、それらは、構造が違うことと、多くの場合定期的に収集されないという点で、メトリックとは異なります。

一般的な種類のログ エントリは、散発的に収集されるイベントです。 イベントは、アプリケーションまたはサービスによって作成され、通常は、単独で完全なコンテキストを示す十分な情報が含まれています。 たとえば、イベントは、特定のリソースが作成または変更されたこと、トラフィックの増加に応答して新しいホストが開始されたこと、またはアプリケーションでエラーが検出されたことを示すことができます。

ログが特に役に立つのは、さまざまなソースからのデータを組み合わせて、複雑な分析や一定期間のトレンド分析を行う場合です。 データの形式は多様である可能性があるため、アプリケーションは、必要な構造を使用するカスタム ログを作成できます。 他の監視データと組み合わせてトレンド分析やその他のデータ分析を行うために、ログ内でメトリックのレプリケートさえ行われます。



### <a name="sources-of-log-data"></a>ログ データのソース
Azure Monitor は、Azure 内とオンプレミス リソースからの両方の、さまざまなソースからログ データを収集できます。 ログ データのソースには、次のものがあります。

- Azure リソースからの、それらの構成と正常性に関する情報を含む[アクティビティ ログ](collect-activity-logs.md)と、それらの操作に関する分析情報を提供する[診断ログ](../../azure-monitor/platform/diagnostic-logs-stream-log-store.md)。
- ユーザーが構成する[データ ソース](data-sources.md)に応じて、ゲスト オペレーティング システムやアプリケーションから Azure Monitor にテレメトリを送信する、[Windows](agent-windows.md) 仮想マシンと [Linux](../learn/quick-collect-linux-computer.md) 仮想マシン上のエージェント。
- [Application Insights](https://docs.microsoft.com/azure/application-insights/) によって収集されるアプリケーション データ。
- 特定のアプリケーションまたはサービスに関する分析情報を提供する、コンテナーの洞察、VM Insights、リソース グループの分析情報などの[監視ソリューション](../insights/solutions.md)または機能からのデータ。
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/) によって収集されたセキュリティ データ。
- Azure リソースからの[メトリック](#metrics)。 これにより、93 日より長くメトリックを保存し、それを他のログ データと共に分析することができます。
- [Azure Storage](azure-storage-iis-table.md) に書き込まれたテレメトリ。
- [HTTP データ コレクター API](data-collector-api.md) を使用する任意の REST API クライアントから、または [Azure Logic App](https://docs.microsoft.com/azure/logic-apps/) ワークフローからのカスタム データ。

![ログの概要](media/data-collection/logs-overview.png)




### <a name="what-can-you-do-with-logs"></a>ログを使用して行えること
ログを使用して実行できるタスクには、以下が含まれます。

- Azure portal で [Log Analytics](../log-query/get-started-portal.md) を使用して、ログ データを分析するクエリを記述します。  テーブルまたはグラフとして表示される結果を [Azure ダッシュ ボード](../../azure-portal/azure-portal-dashboards.md)にピン留めします。
- クエリの結果が特定の結果に一致するときに、通知を送信するか[自動化されたアクション](action-groups.md)を実行する、[ログ警告ルール](alerts-log.md)を構成します。
- [Logic Apps](~/articles/logic-apps/index.yml) を使用して、ログ データに基づくワークフローを構築します。
- クエリの結果を [Power BI](powerbi.md) にエクスポートし、さまざまな視覚化を使用して Azure の外部のユーザーと共有します。
- コマンドラインまたはカスタム アプリケーションから、[PowerShell コマンドレット](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/?view=azurermps-6.8.1)または [REST API](https://dev.loganalytics.io/) を使用して、メトリック値にアクセスします。

### <a name="viewing-log-data"></a>ログ データの表示
Azure Monitor のすべてのログ データは、[データ エクスプローラーのクエリ言語](../log-query/get-started-queries.md)で書かれた[ログ クエリ](../log-query/log-query-overview.md)を使用して取得できます。これにより、収集されたデータを迅速に取得、統合、および分析できます。 [Log Analytics](../log-query/portals.md) を使用して、Azure portal でクエリを記述してテストします。 結果は、対話形式で操作したり、ダッシュボードにピン留めして他の視覚化と一緒に表示したりできます。 [Azure monitoring REST API](../../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md) を使用してログを取得することもできます。

> [!IMPORTANT]
> Application Insights からのデータは、Azure Monitor の他のログ データとは別のパーティションに格納されます。 これは他のログ データと同じ機能をサポートしていますが、このデータにアクセスするには、[Application Insights コンソール](/azure-monitor/app/analytics.md)または [Application Insights API](https://dev.applicationinsights.io/) を使用する必要があります。 [リソース間のクエリ](../log-query/cross-workspace-query.md)を使用して、その他のログ データと一緒にアプリケーション データを分析することができます。

![ログ](media/data-collection/logs.png)


## <a name="convert-monitoring-data"></a>監視データを変換する

### <a name="metrics-to-logs"></a>メトリックからログへ
メトリックをログにコピーし、Azure Monitor の高度なクエリ言語を使用して他の種類のデータと一緒に複雑な分析を実行することができます。 さらに、ログ データは、メトリックよりも長期間保持できます。それにより、一定期間のトレンド分析を実行できます。 ほぼリアルタイムの分析とアラートをサポートする場合はメトリックを使用し、他のデータと共にトレンド分析や分析を行う場合はログを使用します。

Azure リソースからメトリックを収集するためのガイダンスについては、[Azure Monitor で Azure サービスのログとメトリックを使用できるように収集する方法](collect-azure-metrics-logs.md)に関するページを参照してください。 Azure PaaS リソースからのリソースのメトリックを収集するためのガイダンスについては、[Azure Monitor を使用した Azure PaaS リソース メトリックの収集を構成する方法](collect-azurepass-posh.md)に関するページを参照してください。

### <a name="logs-to-metrics"></a>ログからメトリックへ
上記のように、メトリックはログよりも反応が早いため、短い待機時間と低コストでアラートを作成できます。 大量の数値データがログとして格納されます。これらのデータは、メトリックに適している可能性がありながらもメトリックとして Azure Monitor に格納されません。  一般的な例は、エージェントと管理ソリューションから収集されるパフォーマンス データです。 これらの値の一部をメトリックにコピーして、メトリックス エクスプローラーでアラートの発行と分析を行うために使用できます。

この機能の説明については、「[Azure Monitor でのログのメトリック アラートの作成](../../azure-monitor/platform/alerts-metric-logs.md)」を参照してください。 サポートされる値の一覧は、「[Azure Monitor のサポートされるメトリック](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)」に記載されています。

## <a name="stream-data-to-external-systems"></a>外部システムにデータをストリーム配信する
Azure のツールを使用した監視データの分析に加え、監視データをセキュリティ情報およびイベントの管理 (SIEM) 製品などの外部ツールに転送する要件がある場合もあります。 この転送は一般に、[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) を介して、監視対象リソースから直接行われます。 

さまざまな種類の監視データのガイダンスについては、「[外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- Azure のさまざまなリソースで[入手できる監視データ](data-sources.md)を確認します。
