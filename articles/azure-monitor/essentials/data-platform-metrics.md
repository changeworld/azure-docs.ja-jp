---
title: Azure Monitor のメトリック | Microsoft Docs
description: ほぼリアルタイムのシナリオをサポートできる軽量な監視データである、Azure Monitor のメトリックについて説明します。
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2021
ms.author: bwren
ms.openlocfilehash: 3986e59965842166ce675cd778d04984896d671d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458531"
---
# <a name="azure-monitor-metrics-overview"></a>Azure Monitor メトリックの概要
Azure Monitor メトリックは、[監視対象のリソース](../monitor-reference.md)から時系列データベースに数値データを収集する Azure Monitor の機能です。 メトリックは、一定の間隔で収集される数値であり、特定の時刻におけるシステムの何らかの特性を表しています。 

Azure Monitor のメトリックは軽量であり、ほぼリアルタイムのシナリオに対応しているので、問題の警告や迅速な検出に役立ちます。 メトリック エクスプローラーを使用すると、対話形式で分析することができます。値がしきい値を超えるときにアラートで事前に通知したり、ブックやダッシュボードで視覚化したりすることができます。

> [!NOTE]
> Azure Monitor メトリックは、Azure Monitor をサポートするデータ プラットフォームの片方です。 もう半分は、ログとパフォーマンス データを収集して整理し、そのデータを豊富なクエリ言語で分析できる [Azure Monitor ログ](../logs/data-platform-logs.md)です。 
>
> メトリック機能では、特定の構造に数値データのみを格納できるのに対し、ログ機能ではさまざまなデータ型 (それぞれ独自の構造を持つ) を格納できます。 メトリック データの分析に使用できないログ クエリを使用して、ログ データで複雑な分析を実行することもできます。

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Azure Monitor のメトリックでできること
Azure Monitor のメトリック機能を使用できる方法を次の表に示します。

|  | 説明 |
|:---|:---|
| **分析** | [メトリックス エクスプローラー](metrics-charts.md)を使用して、収集されたメトリックをグラフで分析し、さまざまなリソースからのメトリックを比較します。 |
| **Alert** | メトリックがしきい値を超えたときに、通知を送信または[自動化されたアクション](../alerts/action-groups.md)を実行する[メトリック アラート ルール](../alerts/alerts-metric.md)を構成します。 |
| **視覚化** | メトリックス エクスプローラーのグラフを [Azure ダッシュボード](../app/tutorial-app-dashboards.md)にピン留めします。<br>[ブック](../visualize/workbooks-overview.md)を作成し、対話形式のレポートに複数のデータ セットを結合します。 クエリの結果を [Grafana](../visualize/grafana-plugin.md) にエクスポートし、そのダッシュボード機能を活用して、他のデータ ソースと結合します。 |
| **自動化** |  [自動スケーリング](../autoscale/autoscale-overview.md)を使用して、しきい値を超えるメトリック値に基づいてリソースを増加または減少させます。 |
| **取得** | [PowerShell コマンドレット](/powershell/module/az.monitor)を使用して、コマンド ラインからメトリック値にアクセスします。<br>[REST API](./rest-api-walkthrough.md) を使用して、カスタム アプリケーションからメトリック値にアクセスします。<br>[Azure CLI](/cli/azure/monitor/metrics) を使用して、コマンド ラインからメトリック値にアクセスします。 |
| **エクスポート** | [メトリックをログにルーティング](./resource-logs.md#send-to-azure-storage)して、Azure Monitor メトリックのデータと Azure Monitor ログのデータを一緒に分析し、93 日間より長くメトリック値を保存します。<br>メトリックを [Event Hub](./stream-monitoring-data-event-hubs.md) にストリーミングして、外部システムにルーティングします。 |
| **Archive** | コンプライアンス、監査、オフライン レポートの目的で、リソースのパフォーマンスや正常性の履歴を[アーカイブ](./platform-logs-overview.md)します。 |

![メトリックのソースと使用を示す図。](media/data-platform-metrics/metrics-overview.png)

## <a name="data-collection"></a>データ コレクション
Azure Monitor では、次のソースからメトリックを収集します。 Azure Monitor メトリック データベースでこれらのメトリックが収集されると、そのソースとは無関係に一緒に評価できます。

- **Azure リソース**。 プラットフォームのメトリックは Azure リソースによって作成され、リソースの正常性とパフォーマンスについての可視化を提供します。 リソースの種類ごとに[別個のメトリックのセット](./metrics-supported.md)が作成され、必要な構成はありません。 メトリックの定義で特に指定がない限り、プラットフォーム メトリックは 1 分間隔で Azure リソースから収集されます。 

- **アプリケーション**。 監視対象のアプリケーションのメトリックが Application Insights によって作成され、パフォーマンスの問題を検出し、アプリケーションがどのように使用されているかの傾向を追跡するために役立ちます。 値には、_サーバー応答時間_ や _ブラウザー例外_ などが含まれます。

- **仮想マシン エージェント**。 メトリックは、仮想マシンのゲスト オペレーティング システムから収集されます。 Windows 仮想マシンの場合は [Windows Diagnostic Extension](../agents/diagnostics-extension-overview.md) を使用し、Linux 仮想マシンの場合は [InfluxData Telegraf エージェント](https://www.influxdata.com/time-series-platform/telegraf/)を使用して、ゲスト OS メトリックを有効にできます。

- **カスタム メトリック**。 自動的に使用できる標準メトリックに加えて、メトリックを定義することができます。 Application Insights によって監視される[カスタム メトリックをアプリケーション内で定義](../app/api-custom-events-metrics.md)したり、[カスタム メトリック API](./metrics-store-custom-rest-api.md) を使用して Azure サービスのカスタム メトリックを作成したりできます。

Azure Monitor メトリックにデータを送信できるデータ ソースの完全なリストについては、「[Azure Monitor によって監視される内容](../monitor-reference.md)」を参照してください。

## <a name="metrics-explorer"></a>メトリックス エクスプローラー
[メトリックス エクスプ ローラー](metrics-charts.md)を使用して、メトリック データベース内のデータを対話的に分析し、一定期間にわたる複数のメトリックの値をグラフにします。 グラフをダッシュボードにピン留めして、他の視覚化と一緒に表示できます。 [Azure monitoring REST API](./rest-api-walkthrough.md) を使用してメトリックを取得することもできます。

![サーバー要求、サーバーの応答時間、失敗した要求を示すメトリックス エクスプローラーのサンプル グラフのスクリーンショット。](media/data-platform-metrics/metrics-explorer.png)

詳細については、「[Azure Monitor メトリックス エクスプローラーの概要](./metrics-getting-started.md)」を参照してください。

## <a name="data-structure"></a>データ構造
Azure Monitor のメトリックによって収集されるデータは、タイムスタンプ付きデータの分析に最適化された時系列データベースに格納されます。 メトリック値の各セットは、次のプロパティを持つ時系列です。

* 値が収集された時刻。
* 値が関連付けられるリソース。
* メトリックのカテゴリのように機能する名前空間。
* メトリック名。
* 値そのもの。
* [複数のディメンション](#multi-dimensional-metrics) (存在する場合)。 カスタム メトリックは 10 個のディメンションに制限されています。

## <a name="multi-dimensional-metrics"></a>多次元メトリック
メトリック データの課題の 1 つは、収集された値のコンテキストを提供するための情報が限定されている場合が多いことです。 Azure Monitor では、多次元メトリックを使用してこの課題に対処します。 

メトリックのディメンションは、メトリックの値を記述するための追加データを保持する名前と値のペアです。 たとえば、_使用可能なディスク領域_ というメトリックは、_C:_ 、_D:_ という値がある _ドライブ_ と呼ばれるディメンションを持つことができます。 そのディメンションにより、すべてのドライブまたは個別のドライブで使用可能なディスク領域の表示が可能になります。

次の例は、_ネットワーク スループット_ という名前の架空のメトリック用の 2 つのデータセットを示しています。 最初のデータセットにはディメンションがありません。 2 番目のデータセットは、_IP_ と _方向_ という 2 つのディメンションの値を示しています。

### <a name="network-throughput"></a>ネットワーク スループット

| Timestamp     | メトリック値 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 Kbps |
| 8/9/2017 8:15 | 1,141.4 Kbps |
| 8/9/2017 8:16 | 1,110.2 Kbps |

このディメンションのないメトリックは、「特定の時点でのネットワークのスループットは?」のような基本的な質問にのみ答えることができます。

### <a name="network-throughput-and-two-dimensions-ip-and-direction"></a>ネットワーク スループットと 2 つのディメンション ("IP" と "方向")

| Timestamp     | ディメンション "IP"   | ディメンション "方向" | メトリック値|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | 方向 = "送信"    | 646.5 Kbps |
| 8/9/2017 8:14 | IP="192.168.5.2" | 方向 = "受信" | 420.1 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 方向 = "送信"    | 150.0 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 方向 = "受信" | 115.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 方向 = "送信"    | 515.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 方向 = "受信" | 371.1 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 方向 = "送信"    | 155.0 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 方向 = "受信" | 100.1 Kbps |

このメトリックは、次のような質問に答えることができます。「各 IP アドレスのネットワーク スループットは?」 や「データの送信量と受信量は?」です。 多次元メトリックは、ディメンションを持たないメトリックに比べ、分析と診断に使用できる多数の値を提供します。

### <a name="viewing-multi-dimensional-performance-counter-metrics-in-metrics-explorer"></a>メトリック エクスプローラーでの多次元パフォーマンス カウンター メトリックの表示 
従来のゲスト メトリック API を使用して、アスタリスク (\*) を含むパフォーマンス カウンター メトリックを Azure Monitor に送信することはできません。 アスタリスクを含むメトリックは、従来のメトリックではサポートされない多次元メトリックであるため、この API では表示できません。

Azure 診断の拡張機能を使用して多次元ゲスト OS パフォーマンス カウンター メトリックを構成および表示するには以下を行います。

1. 仮想マシンの **[診断設定]** ページに移動します。
2. **[パフォーマンス カウンター]** タブを選択します。 
3. **[カスタム]** を選択して、収集したいパフォーマンス カウンターを構成します。

   ![[診断設定] ページの [パフォーマンス カウンター] セクションのスクリーンショット。](media/data-platform-metrics/azure-monitor-perf-counter.png)

4. **[シンク]** を選択します。 次に、 **[有効]** を選択して Azure Monitor にデータを送信します。

   ![[診断設定] ページの [シンク] セクションのスクリーンショット。](media/data-platform-metrics/azure-monitor-sink.png)

5. Azure Monitor でメトリックを表示するには、 **[メトリック名前空間]** ドロップダウンで **[仮想マシンのゲスト]** を選択します。

   ![[メトリック名前空間] のスクリーンショット。](media/data-platform-metrics/vm-guest-namespace.png)

6. **[分割の適用]** を選択し、詳細を入力して、インスタンス別にメトリックを分割します。 その後、構成でアスタリスクによって表される有効値ごとに分類されたメトリックが表示できます。 この例では、アスタリスクは、論理ディスク ボリュームと合計を表します。

   ![インスタンス別のメトリックの分割のスクリーンショット。](media/data-platform-metrics/split-by-instance.png)

## <a name="retention-of-metrics"></a>メトリックの保有期間
Azure のほとんどのリソースでは、プラットフォーム メトリックは 93 日間保存されます。 ただし、例外があります。

- **クラシック ゲスト OS メトリック**: これらは [Windows 診断拡張機能](../agents/diagnostics-extension-overview.md) または [Linux 診断拡張機能](../../virtual-machines/extensions/diagnostics-linux.md)によって収集され、Azure ストレージ アカウントにルーティングされるパフォーマンス カウンターです。 これらのメトリックは少なくとも 14 日間は保持されます。ただし、有効期限はストレージ アカウントに記載されていません。 
  
  パフォーマンス上の理由により、ポータルでは、表示されるデータの量をボリュームに基づいて制限しています。 したがって、作成されるデータの量が多くない場合、ポータルにより取得される実際の日数は 14 日間より長くなる場合があります。 

- **Azure Monitor メトリックに送信されたゲスト OS メトリック**: これらは、[Windows 診断拡張機能](../agents/diagnostics-extension-overview.md)によって収集され、[Azure Monitor データ シンク](../agents/diagnostics-extension-overview.md#data-destinations)に送信されるか、あるいは Linux マシン上の [InfluxData Telegraf エージェント](https://www.influxdata.com/time-series-platform/telegraf/)、またはデータ収集ルールを使用して新しい [Azure Monitor エージェント](../agents/azure-monitor-agent-overview.md)によって収集されるパフォーマンス カウンターです。 これらのメトリックの保有期間は 93 日です。

- **Log Analytics エージェントによって収集されたゲスト OS メトリック:** これらは、Log Analytics エージェントによって収集され、Log Analytics ワークスペースに送信されるパフォーマンス カウンターです。 これらのメトリックの保有期間は 31 日で、最大 2 年間まで延長できます。

- **Application Insights ログベースのメトリック** [ログ ベースのメトリック](../app/pre-aggregated-metrics-log-metrics.md)は、バックグラウンドでログ クエリに変換されます。 その保有期間は可変で、基になるログ内のイベントの保有期間 (31 日～ 2 年間) と一致します。 Application Insights リソースの場合、ログは 90 日間保存されます。

> [!NOTE]
> 長期的な傾向を見るために、[Azure Monitor リソースのプラットフォーム メトリックを Log Analytics ワークスペースに送信](./resource-logs.md#send-to-azure-storage)できます。

> [!NOTE]
> 前述のとおり、Azure のほとんどのリソースでは、プラットフォーム メトリックは 93 日間保存されます。 ただし、( **[メトリック]** タイルで) 1 つのグラフでクエリを実行できるデータは最大 30 日です。 この制限は、ログ ベースのメトリックには適用されません。 
>
> 空のグラフが表示される場合、またはグラフにメトリック データの一部のみが表示される場合は、日時指定の開始日と終了日の間隔が、30 日を超えていないことを確認します。 30 日間の間隔を選択したら、グラフを[パン](./metrics-charts.md#pan)して完全なリテンション期間を表示できます。

## <a name="next-steps"></a>次のステップ

- [Azure Monitor データ プラットフォーム](../data-platform.md)の詳細を確認します。
- [Azure Monitor のログ データ](../logs/data-platform-logs.md)について確認します。
- Azure のさまざまなリソースで[入手できる監視データ](../agents/data-sources.md)を確認します。