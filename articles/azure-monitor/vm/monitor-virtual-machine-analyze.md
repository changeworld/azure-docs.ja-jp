---
title: 'Azure Monitor を使用して仮想マシンを監視する: 監視データの分析'
description: 仮想マシンの正常性とパフォーマンスの分析に使用できる Azure Monitor のさまざまな機能について説明します。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 9eb84e0b685a41cd0ead7aae1feb38c475465f83
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325674"
---
# <a name="monitor-virtual-machines-with-azure-monitor-analyze-monitoring-data"></a>Azure Monitor を使用して仮想マシンを監視する: 監視データの分析
この記事は、シナリオ「[Azure Monitor で仮想マシンとそのワークロードを監視する](monitor-virtual-machine.md)」の一部です。 これは、仮想マシンの構成を完了した後に仮想マシンの監視データを分析する方法について説明しています。

仮想マシンで VM insights を有効にすると、データを分析できるようになります。 この記事では、仮想マシンの正常性とパフォーマンスの分析に使用できる Azure Monitor のさまざまな機能について説明します。 これらの機能のいくつかは、分析するマシンが単一か複数かによって、エクスペリエンスが異なります。 ここでは、使用されているエクスペリエンスに応じて、各機能の固有の動作とともに各エクスペリエンスについて説明します。

> [!NOTE]
> この記事には、Azure Monitor と VM insights によって収集されるデータの分析に関するガイダンスが含まれています。 仮想マシンで実行されているワークロードを監視するように構成するデータについては、[ワークロードの監視](monitor-virtual-machine-workloads.md)に関するページを参照してください。

## <a name="single-machine-experience"></a>単一マシンのエクスペリエンス
各 Azure 仮想マシンと Azure Arc 対応サーバーの Azure portal でメニューの **[監視]** セクションから、単一マシンの分析エクスペリエンスにアクセスします。 これらのオプションを使用することで、表示するデータをそのマシンに制限するか、少なくとも初期フィルターを設定することができます。 このようにして、特定のコマシンに集中して、現在のパフォーマンスとその傾向を時系列で表示し、発生している可能性がある問題を特定することができます。

:::image type="content" source="media/monitor-virtual-machines/vm-menu.png" alt-text="Azure portal での VM の分析を示すスクリーンショット。" lightbox="media/monitor-virtual-machines/vm-menu.png":::

- **概要ページ**: 仮想マシンのホストの [プラットフォームのメトリック](../essentials/data-platform-metrics.md)を表示するには、 **[監視]** タブを選択します。 CPU、ネットワーク、ディスクなど、重要なメトリックのさまざまな期間における傾向を簡単に把握できます。 ただし、これらはホストのメトリックであるため、ゲスト オペレーティング システムからのカウンター (メモリなど) は含まれません。 さまざまな集計を実行できる[メトリックス エクスプローラー](../essentials/metrics-getting-started.md)でこのデータを操作するグラフを選択し、分析用のカウンターを追加します。
- **アクティビティ ログ**: 現在の仮想マシンに対してフィルター処理された [アクティビティ ログ](../essentials/activity-log.md#view-the-activity-log)のエントリが表示されます。 構成の変更やいつ停止および開始されたかなど、マシンの最近のアクティビティを表示するには、このログを使用します。 
- **分析情報**: 現在選択されている仮想マシンのマップを使用して [VM insights](../vm/vminsights-overview.md) を開きます。 マップには、マシン上で実行中のプロセス、他のマシンへの依存関係、および外部プロセスが表示されます。 単一のマシンについてマップ ビューを使用する方法の詳細については、「[VM insights のマップ機能を使用してアプリケーション コンポーネントを把握する](vminsights-maps.md#view-a-map-from-a-vm)」を参照してください。

    **[パフォーマンス]** タブを選択すると、さまざまな期間の重大なパフォーマンス カウンターの傾向が表示されます。 [仮想マシン] メニューから VM insights を開くと、各ディスクの詳細なメトリックを含むテーブルも表示されます。 単一のマシンについてマップ ビューを使用する方法の詳細については、[VM insights を使用したパフォーマンスのグラフ化](vminsights-performance.md#view-performance-directly-from-an-azure-vm)に関するページを参照してください。 

- **アラート**: 現在の仮想マシンの [アラート](../alerts/alerts-overview.md)が表示されます。 これらのアラートは、ターゲット リソースとしてマシンのみを使用するため、他のアラートが関連付けられている可能性があります。 すべてのリソースのアラートを表示するには、[Azure Monitor] メニューの **[アラート]** オプションを使用する必要がある場合があります。 詳細については、「[Azure Monitor を使用して仮想マシンを監視する: アラート](monitor-virtual-machine-alerts.md)」を参照してください。
- **メトリック**: スコープをマシンに設定して、メトリックス エクスプローラーを開きます。 このオプションは、 **[概要]** ページからパフォーマンス グラフを 1 つ選択するのと同じですが、メトリックがまだ追加されていない点が異なります。
- **診断設定**: 現在の仮想マシンに対して [診断拡張機能](../agents/diagnostics-extension-overview.md)を有効にして構成します。 このオプションは、他の Azure リソースの **診断設定** オプションとは異なります。 診断拡張機能は、Azure Event Hubs または Azure Storage にデータを送信する必要がある場合にのみ有効にします。
- **Advisor の推奨事項**: [Azure Advisor](../../advisor/index.yml) からの現在の仮想マシンに関する推奨事項が表示されます。
- **ログ**: [スコープ](../logs/scope.md)を現在の仮想マシンに設定して、[Log Analytics](../logs/log-analytics-overview.md) を開きます。 さまざまな既存のクエリから選択して、このマシンのみのログとパフォーマンス データにドリルダウンすることができます。 
- **接続モニター**: [Network Watcher 接続モニター](../../network-watcher/connection-monitor-overview.md)を開いて、現在の仮想マシンと他の仮想マシンとの間の接続を監視します。 
- **ブック**: 単一マシンの VM insights ブックでブック ギャラリーを開きます。 個々のマシン向けに設計された VM insights ブックの一覧については、「[VM insights ブック](vminsights-workbooks.md#vm-insights-workbooks)」を参照してください。

## <a name="multiple-machine-experience"></a>複数マシンのエクスペリエンス
各 Azure 仮想マシンと Azure Arc 対応サーバーの Azure portal で **[監視]** メニューから、複数マシンの分析エクスペリエンスにアクセスします。 これらのオプションによって、すべてのデータへのアクセスが提供されるため、比較対象の仮想マシンを選択できるようになります。

:::image type="content" source="media/monitor-virtual-machines/monitor-menu.png" alt-text="Azure portal での複数の VM の分析を示すスクリーンショット。" lightbox="media/monitor-virtual-machines/monitor-menu.png":::

- **アクティビティ ログ**: すべてのリソースに対してフィルター処理された [アクティビティ ログ](../essentials/activity-log.md#view-the-activity-log)のエントリが表示されます。 すべてのマシンのイベントを表示するには、仮想マシンまたは仮想マシン スケール セットの **リソースの種類** のフィルターを作成します。
- **アラート**: すべてのリソースの [アラート](../alerts/alerts-overview.md)を表示します。これには、仮想マシンに関連するものの、ワークスペースに関連付けられているアラートが含まれます。 すべてのマシンのアラートを表示するには、仮想マシンまたは仮想マシン スケール セットの **リソースの種類** のフィルターを作成します。 
- **メトリック**: スコープを選択せずに [メトリックス エクスプローラー](../essentials/metrics-getting-started.md)を開きます。 この機能は、複数のマシン間で傾向を比較する場合に特に便利です。 まとめて分析するマシンのグループをすばやく追加するには、サブスクリプションまたはリソース グループを選択します。
- **ログ**: [スコープ](../logs/scope.md)をワークスペースに設定して、[Log Analytics](../logs/log-analytics-overview.md) を開きます。 さまざまな既存のクエリから選択して、すべてのマシンのログとパフォーマンス データにドリルダウンすることができます。 または、追加の分析を実行するためのカスタム クエリを作成することもできます。
- **ブック**: 複数マシンの VM insights ブックでブック ギャラリーを開きます。 複数のマシン向けに設計された VM insights ブックの一覧については、「[VM insights ブック](vminsights-workbooks.md#vm-insights-workbooks)」を参照してください。 
- **Virtual Machines**: **[概要]** タブを開いた状態で [VM insights](../vm/vminsights-overview.md) を開きます。 このアクションによって、Azure サブスクリプション内のすべてのマシンが表示され、監視対象のマシンが特定されます。 このビューを使用して、まだ監視対象になっていない個々のマシンをオンボードします。

    さまざまな期間における複数のマシンの重要なパフォーマンス カウンターの傾向を比較するには、 **[パフォーマンス]** タブを選択します。 ビューに含めるサブスクリプションまたはリソース グループ内のすべてのマシンを選択します。 単一のマシンについてマップ ビューを使用する方法の詳細については、[VM insights を使用したパフォーマンスのグラフ化](vminsights-performance.md#view-performance-directly-from-an-azure-vm)に関するページを参照してください。

    マシンで実行中のプロセス、マシン間の依存関係、および外部プロセスを表示するには、 **[マップ]** タブを選択します。 サブスクリプションまたはリソース グループ内のすべてのマシンを選択するか、単一マシンのデータを検査します。 複数のマシンについてマップ ビューを使用する方法の詳細については、「[VM insights のマップ機能を使用してアプリケーション コンポーネントを把握する](vminsights-maps.md#view-a-map-from-azure-monitor)」を参照してください。 
 
## <a name="compare-metrics-and-logs"></a>メトリックとログを比較する
Azure Monitor の多くの機能については、使用されるさまざまな種類のデータとその格納場所について理解する必要はありません。 たとえば、パフォーマンス ビュー、マップ ビュー、およびブックへの入力にどのデータが使用されているかを理解していなくても、VM insights を使用できます。 分析しているロジックに集中するだけです。 さらに掘り下げていくには、[メトリック](../essentials/data-platform-metrics.md)と[ログ](../logs/data-platform-logs.md)の違いを理解する必要があります。 Azure Monitor の機能によって、使用するデータの種類が異なります。 特定のシナリオに使用するアラートの種類は、そのデータを特定の場所で使用できるかどうかによって異なります。

Azure Monitor を初めて使用するときは、この詳細レベルが分かりにくい場合があります。 データの種類の違いを理解するには、次の情報を参考にしてください。

- イベントなどの数値以外のデータは、ログに格納されます。 メトリックには、一定の間隔でサンプリングされた数値データのみを含めることができます。
- 数値データはメトリックとログの両方に格納して、さまざまな方法で分析したり、さまざまな種類のアラートをサポートしたりすることができます。
- ゲスト オペレーティング システムからのパフォーマンス データは、Log Analytics エージェントを使用して、VM insights によってログに送信されます。
- ゲスト オペレーティング システムからのパフォーマンス データは、Azure Monitor エージェントによってメトリックに送信されます。

> [!NOTE]
> Azure Monitor エージェントは、メトリックとログの両方にデータを送信します。 このシナリオでは、Log Analytics エージェントによって VM insights に現在必要なデータがログに送信されるため、メトリックにのみ使用されます。 VM insights で Azure Monitor エージェントが使用されている場合、このシナリオは、Log Analytics エージェントを削除するように更新されます。

## <a name="analyze-data-with-vm-insights"></a>VM insights を使用してデータを分析する
VM insights には、監視対象マシンの操作の状態、時間の経過に伴うパフォーマンスの傾向、およびマシンとプロセス間の依存関係をすばやく把握するのに役立つ複数のパフォーマンス グラフが含まれています。 また、Log Analytics ワークスペースで収集されたプロパティやイベントなど、監視対象マシンのさまざまな側面の統合ビューも用意されています。

**[概要]** タブには、Azure サブスクリプション内のすべてのマシンが表示され、監視対象のマシンが識別されます。 このビューを使用して、監視されていないマシンをすばやく特定し、まだ監視対象になっていない個々のマシンをオンボードすることができます。

:::image type="content" source="media/monitor-virtual-machines/vminsights-get-started.png" alt-text="VM insights の概要を示すスクリーンショット。" lightbox="media/monitor-virtual-machines/vminsights-get-started.png":::

**パフォーマンス** ビューには、マシンのパフォーマンスを判断するのに役立ついくつかの主要業績評価指標 (KPI) を含む複数のグラフが含まれています。 グラフには、一定期間のリソース使用率が表示されます。 これらを使用して、ボトルネックを特定したり、異常を確認したり、各マシンを一覧表示するパースペクティブに切り替えて、選択したメトリックに基づいてリソース使用率を表示したりできます。 パフォーマンス ビューの使用方法の詳細については、[VM insights を使用したパフォーマンスのグラフ化](vminsights-performance.md)に関するページを参照してください。

:::image type="content" source="media/monitor-virtual-machines/vminsights-performance.png" alt-text="VM insights のパフォーマンスを示すスクリーンショット。" lightbox="media/monitor-virtual-machines/vminsights-performance.png":::

**マップ** ビューを使用すると、マシン上の実行中のプロセスと、他のマシンや外部プロセスへの依存関係を表示できます。 ビューの時間枠を変更して、これらの依存関係が別の期間から変更されたかどうかを判断できます。 マップ ビューの使用方法の詳細については、「[VM insights のマップ機能を使用してアプリケーション コンポーネントを把握する](vminsights-maps.md)」を参照してください。

:::image type="content" source="media/monitor-virtual-machines/vminsights-map.png" alt-text="VM insights マップを示すスクリーンショット。" lightbox="media/monitor-virtual-machines/vminsights-map.png":::

## <a name="analyze-metric-data-with-metrics-explorer"></a>メトリックス エクスプローラーを使用してメトリック データを分析する
メトリックス エクスプローラーを使用すると、グラフをプロットしたり、傾向を視覚的に関連付けたり、メトリック値の急上昇と急降下を調べたりすることができます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../essentials/metrics-getting-started.md)」を参照してください。 

仮想マシンによって 3 つの名前空間が使用されます。

| 名前空間 | 説明 | 要件 |
|:---|:---|:---|
| 仮想マシンのホスト | すべての Azure 仮想マシンについて、ホストのメトリックが自動的に収集されます。 メトリックの詳細なリストは、[Microsoft.Compute/virtualMachines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines) にあります。 | 自動的に収集され、構成は必要ありません。 |
| ゲスト (クラシック) | ゲスト オペレーティング システムとアプリケーションのパフォーマンス データの限られたセット。 メトリックス エクスプローラーで使用できますが、メトリック アラートなどの他の Azure Monitor 機能では使用できません。  | [診断の拡張機能](../agents/diagnostics-extension-overview.md)がインストールされていること。 データは Azure Storage から読み取られます。  |
| 仮想マシンのゲスト | メトリックを使用するすべての Azure Monitor 機能で使用可能な、ゲスト オペレーティング システムとアプリケーションのパフォーマンス データ。 | [データ収集ルール](../agents/data-collection-rule-overview.md)を使用してインストールされた [Azure Monitor エージェント](../agents/azure-monitor-agent-overview.md)。 |

## <a name="analyze-log-data-with-log-analytics"></a>Log Analytics を使用してログ データを分析する
Log Analytics を使用すると、ログ データのカスタム分析を実行できます。 VM insights でビューを作成するために使用されるデータをさらに掘り下げる場合は、Log Analytics を使用します。 そのデータのさまざまなロジックと集計を分析したり、Microsoft Defender for Cloud と Microsoft Sentinel によって収集されたセキュリティ データを正常性と可用性のデータに関連付けたり、[ワークロード](monitor-virtual-machine-workloads.md)用に収集されたデータを操作したりすることができます。

Log Analytics を使用するために、必ずしもログ クエリの記述方法について理解している必要はありません。 複数の事前定義済みのクエリがあり、そこからクエリを選択して、変更せずに実行するか、カスタム クエリの開始として使用することができます。 Log Analytics 画面の上部にある **[クエリ]** を選択して、 **[リソースの種類]** が **仮想マシン** または **仮想マシン スケール セット** のクエリを表示します。 これらのクエリの使用方法の詳細については、「[Azure Monitor Log Analytics でのクエリの使用](../logs/queries.md)」を参照してください。 Log Analytics を使用してクエリを実行し、その結果を操作する方法のチュートリアルについては、「[Log Analytics のチュートリアル](../logs/log-analytics-tutorial.md)」を参照してください。

:::image type="content" source="media/monitor-virtual-machines/vm-queries.png" alt-text="仮想マシンのクエリを示すスクリーンショット。" lightbox="media/monitor-virtual-machines/vm-queries.png":::

**パフォーマンス** または **マップ** のいずれかのビューのプロパティ ペインを使用して、VM insights から Log Analytics を起動すると、選択したコンピューターのデータを含むテーブルが一覧表示されます。 テーブルを選択すると、選択したコンピューターのそのテーブル内のすべてのレコードを返す単純なクエリを使用して Log Analytics が開きます。 これらの結果を使用するか、より複雑な分析のためにクエリを変更します。 [スコープ](../log/../logs/scope.md)がワークスペースに設定されているということは、そのワークスペースを使用しているすべてのコンピューターのデータにアクセスできることを意味します。 

:::image type="content" source="media/monitor-virtual-machines/table-query.png" alt-text="テーブル クエリを示すスクリーンショット。" lightbox="media/monitor-virtual-machines/table-query.png":::

## <a name="visualize-data-with-workbooks"></a>ブックを使用したデータの視覚化
[ブック](../visualize/workbooks-overview.MD)を使用すると、Azure portal で対話型レポートが提供され、さまざまな種類のデータが 1 つのビューに結合されます。 ブックでは、テキスト、 [ログ クエリ](/azure/data-explorer/kusto/query/)、メトリック、パラメーターが、内容豊富な対話型レポートに組み合わされます。 ブックは、同じ Azure リソースにアクセスできる他のチーム メンバーが編集できます。

Workbooks は次のようなシナリオで便利です。

* CPU 使用率、ディスク領域、メモリ、ネットワークの依存関係など、目的のメトリックが事前にわかっていないときに、仮想マシンの使用状況を調べます。 他の使用状況分析ツールとは異なり、ブックを使用すると、複数の種類の視覚化と分析を結合できるため、この種のフリーフォーム探索に適しています。
* 主要なカウンターと他のログ イベントに関するメトリックを示すことによって、最近プロビジョニングされた VM のパフォーマンスをチームに説明します。
* VM のサイズ変更実験の結果を、チームの他のメンバーと共有します。 実験の目標をテキストで説明することができます。 その後、その実験の評価に使用された使用状況メトリックと分析クエリ、および各メトリックが目標を上回ったかまたは下回ったかを明確に表示できます。
* 停止が VM の使用に及ぼす影響を報告し、データ、テキスト説明、および次の手順についての話し合いを組み合わせて将来の停止を防止します。

VM insights には、次のブックが含まれます。 これらのブックは、そのまま使用することも、特定の要件に対応するカスタム ブックを作成するための開始点として使用することもできます。

### <a name="single-virtual-machine"></a>1 つの仮想マシン

| Workbooks | 説明 |
|----------|-------------|
| パフォーマンス | 有効にしたすべての Log Analytics パフォーマンス カウンターを使用する、パフォーマンス ビューのカスタマイズ可能なバージョンが提供されます。 | 
| 接続 | VM からの受信接続および VM への送信接続に関する詳細なビューが提供されます。 | 

### <a name="multiple-virtual-machines"></a>複数の仮想マシン

| Workbooks | 説明 |
|----------|-------------|
| パフォーマンス | 有効にしたすべての Log Analytics パフォーマンス カウンターを使用する 1 つのブックで、上位 N 位までのリストとグラフ ビューのカスタマイズ可能なバージョンが提供されます。|
| パフォーマンス カウンター | パフォーマンス カウンターの広範なセットに関する上位 N 位までのグラフ ビューが提供されます。 |
| 接続 | 監視対象のマシンからの受信接続およびマシンへの送信接続に関する詳細なビューが提供されます。 |
| アクティブなポート | 選択された期間における、監視対象のマシンのポートにバインドされたプロセスの一覧とそれらのアクティビティが提供されます。 |
| ポートを開く | 監視対象のマシンで開かれているポートの数と、それらの開かれたポートの詳細が提供されます。 |
| 失敗した接続 | 監視対象のマシンで失敗した接続の数、失敗の傾向、失敗の割合が時間と共に増えているかどうかが、表示されます。 |
| [Security and Audit] | 全体的な接続、悪意のある接続、IP エンドポイントの世界的な配置に関して報告する TCP/IP トラフィックの分析が提供されます。 すべての機能を有効にするには、セキュリティの検出を有効にする必要があります。 |
| TCP トラフィック | 監視対象のマシンと、その送信、受信、および合計ネットワーク トラフィックに関するランク付けレポートのグリッド表示とトレンド ライン表示が提供されます。 |
| トラフィックの比較 | 1 つのマシンまたはマシンのグループのネットワーク トラフィックの傾向が比較されます。 |
| Log Analytics エージェント | 異常なワークスペースに接続しているエージェントの数や、エージェントがマシンのパフォーマンスに与える影響など、エージェントの正常性を分析します。 このブックは、他のブックのように VM insights から使用することはできません。 [Azure Monitor] メニューの **[ブック]** にアクセスし、 **[パブリック テンプレート]** を選択します。 |

独自のカスタム ブックを作成する方法については、「[VM Insights のブックを使用して対話型レポートを作成する](vminsights-workbooks.md)」を参照してください。

:::image type="content" source="media/monitor-virtual-machines/workbook-example.png" alt-text="仮想マシンのブックを示すスクリーンショット。" lightbox="media/monitor-virtual-machines/workbook-example.png":::

## <a name="next-steps"></a>次のステップ

* [収集されたデータからアラートを作成する](monitor-virtual-machine-alerts.md)
* [仮想マシンで実行されているワークロードを監視する](monitor-virtual-machine-workloads.md)
