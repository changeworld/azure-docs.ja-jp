---
title: Azure Data Explorer Insights| Microsoft Docs
description: この記事では、Azure Data Explorer Insights の使用方法について説明します。
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: fc40608b7c9faa4acdef999fdef787d787aca720
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132818"
---
# <a name="azure-data-explorer-insights"></a>Azure Data Explorer Insights

Azure Data Explorer Insights は、クラスターのパフォーマンス、操作、使用状況、障害の統合されたビューを提供することによって、クラスターの包括的な監視を可能にします。

次の特長があります。

-    **大規模な分析観点**。 クラスターの主要メトリックのスナップショット ビューにより、クエリ、インジェスト、エクスポート操作のパフォーマンスの追跡が容易になります。
-   **ドリルダウン分析**。 特定の Azure Data Explorer クラスターをドリルダウンして詳細な分析を実行できます。
-    **カスタマイズ**。 表示するメトリックを変更し、制限に合わせてしきい値を変更または設定して、独自のカスタム ブックとして保存できます。 ブック内のグラフは、Azure ダッシュボードにピン留めできます。

この記事は、Azure Data Explorer Insights をオンボードして使用する方法を理解するために役立ちます。

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Azure Monitor から表示する (大規模な分析観点)

クラスターの主なパフォーマンス メトリックは、Azure Monitor から確認できます。 それらのメトリックには、サブスクリプション内の複数クラスターのクエリ、インジェスト、エクスポート操作についての情報が含まれます。 これらは、パフォーマンスの問題を特定するための助けにすることができます。

すべてのサブスクリプションにまたがってクラスターのパフォーマンスを表示するには:

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. 左ペインで **[監視]** を選択します。 **[Insights Hub]** セクションの **[Azure Data Explorer クラスター]** を選択します。

![Azure Data Explorer クラスターのパフォーマンスを表示するための選択内容のスクリーンショット。](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>[概要] タブ

選択したサブスクリプションの **[概要]** タブでは、サブスクリプション内でグループ化されている Azure Data Explorer クラスターの対話型メトリックがテーブルに表示されます。 次のドロップダウン リストから選択するオプションに基づいて、結果をフィルター処理できます。

* **[サブスクリプション]** : Azure Data Explorer クラスターを含むサブスクリプションだけが一覧表示されます。

* **[Azure Data Explorer クラスター]** : 既定では、最大 5 つのクラスターが事前に選択されています。 スコープ セレクターですべてまたは複数のクラスターを選択すると、最大 200 個のクラスターが返されます。

* **[時間の範囲]** : 既定では、選択結果に応じて過去 24 時間の情報が表示されます。

ドロップダウン リストのカウンター タイルには、選択したサブスクリプション内の Azure Data Explorer クラスターの総数のうち、選択されている数が表示されます。 **[キープ アライブ]** 、 **[CPU]** 、 **[Ingestion Utilization]\(インジェスト使用率\)** 、 **[Cache Utilization]\(キャッシュ使用率\)** の各列は条件に基づいて色分けされます。 オレンジで色分けされたセルには、クラスターの持続可能ではない値が含まれています。 

各メトリックが表す内容について理解を深めるために、[Azure Data Explorer のメトリック](/azure/data-explorer/using-metrics#cluster-metrics)に関するドキュメントを参照することをお勧めします。

### <a name="query-performance-tab"></a>[クエリ パフォーマンス] タブ

**[クエリ パフォーマンス]** タブには、クエリ期間、同時クエリの総数、抑えられたクエリの総数が表示されます。

![[クエリ パフォーマンス] タブのスクリーンショット。](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>[Ingestion Performance]\(インジェストのパフォーマンス\) タブ

**[Ingestion Performance]\(インジェストのパフォーマンス\)** タブには、インジェストの待ち時間、成功したインジェストの結果、失敗したインジェストの結果、インジェストの量、イベント ハブおよび IoT ハブの処理されたイベントが表示されます。

[![[Ingestion Performance]\(インジェストのパフォーマンス\) タブのスクリーンショット。](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>[Streaming Ingest Performance]\(ストリーミング インジェストのパフォーマンス\) タブ

**[Streaming Ingest Performance]\(ストリーミング インジェストのパフォーマンス\)** タブでは、平均データ速度、平均期間、要求レートに関する情報が提供されます。

### <a name="export-performance-tab"></a>[Export Performance]\(エクスポートのパフォーマンス\) タブ

**[Export Performance]\(エクスポートのパフォーマンス\)** タブでは、連続エクスポート操作のエクスポートされたレコード、遅延、保留中の数、使用率に関する情報が提供されます。

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Azure Data Explorer クラスター リソースから表示する (ドリルダウン分析)

Azure Data Explorer クラスターから Azure Data Explorer Insights に直接アクセスするには:

1. Azure portal で、 **[Azure Data Explorer クラスター]** を選択します。

2. 一覧から、Azure Data Explorer クラスターを選択します。 [監視] セクションで **[Insights]** を選択します。

これらのビューには、Azure Monitor 分析情報ビュー内から Azure Data Explorer クラスターのリソース名を選択してアクセスすることもできます。

> [!NOTE]
> Azure Data Explorer Insights では、ログとメトリックを組み合わせて、グローバルな監視ソリューションを提供します。 ログベースの視覚化を含めるには、[Azure Data Explorer クラスターの診断ログを有効にし、Log Analytics ワークスペースに送信する](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs)必要があります。 有効にする必要がある診断ログは、**Command**、**Query**、**SucceededIngestion**、**FailedIngestion**、**IngestionBatching**、**TableDetails**、**TableUsageStatistics** です。 (**SucceededIngestion** ログを有効にすると、コストが大きくなる可能性があります。 有効にするのは、インジェストの成功を監視する必要がある場合に限定してください。)

![監視用のログを構成するためのボタンのスクリーンショット。](./media/data-explorer/enable-logs.png)

### <a name="overview-tab"></a>[概要] タブ

**[概要]** タブには、以下が表示されます。

- クラスターの可用性と全体的な状態を表示することで迅速な正常性評価を可能にするメトリック タイル。

- アクティブな [Azure Advisor の推奨事項](/azure/data-explorer/azure-advisor)と[リソースの正常性](/azure/data-explorer/monitor-with-resource-health)状態の概要。

- CPU およびメモリの上位コンシューマーと一意のユーザーの数を経時的に示すグラフ。

[![Azure Data Explorer クラスター リソースからの表示のスクリーンショット。](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

### <a name="key-metrics-tab"></a>[主要な指標] タブ

**[主要な指標]** タブには、クラスターの複数のメトリックの統合ビューが表示されます。 これらは、一般的なメトリック、クエリ関連メトリック、インジェスト関連メトリック、ストリーミング インジェスト関連メトリックでグループ化されています。

[![[主要な指標] タブのグラフのスクリーンショット。](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

### <a name="usage-tab"></a>[使用] タブ

**[Usage]\(使用状況\)** タブでは、クラスターのコマンドとクエリのパフォーマンスの詳細を確認できます。 このタブでは、次のことができます。
 
- クエリを最も多く送信している、または CPU とメモリを最も多く消費しているワークロード グループ、ユーザー、アプリケーションを確認します。 クラスターの処理に最も負荷を掛けているクエリがどのワークロードから送信されているかを把握できます。
- 失敗したクエリで上位のワークロード グループ、ユーザー、およびアプリケーションを特定します。
- ワークロード グループ、ユーザー、およびアプリケーションごとに、過去の 1 日平均 (過去 16 日間) と比較したクエリ数の最近の変化を示します。
- ワークロード グループ、ユーザー、アプリケーション、コマンドの種類別に、クエリ数、メモリ消費量、CPU 消費量の傾向とピークを示します。

**[Usage]\(使用状況\)** タブには、ユーザーが直接実行するアクションが含まれています。 内部クラスター操作は、このタブには含まれません。

[![コマンドとクエリに関連したドーナツ グラフを含む操作ビューのスクリーンショット。](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![クエリとメモリに関連した折れ線グラフを含む操作ビューのスクリーンショット。](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

### <a name="tables-tab"></a>[テーブル] タブ

**[テーブル]** タブには、クラスター内のテーブルの最新および過去のプロパティが表示されます。 最も多くの領域を消費しているテーブルを確認できます。 テーブル サイズ、ホット データ、行数で増加の過程を経時的に追跡することもできます。

### <a name="cache-tab"></a>[キャッシュ] タブ

**[キャッシュ]** タブでは、実際のクエリのルックバック ウィンドウ パターンを分析し、構成済みのキャッシュ ポリシーと比較できます (テーブルごと)。 最も多くのクエリで使用されているテーブルと、まったく照会されていないテーブルを特定し、キャッシュ ポリシーを適宜調整できます。 

特定のテーブルに対するキャッシュ ポリシーの推奨事項が、Azure Advisor に表示される場合があります。 現在は、[メイン Azure Advisor ダッシュボード](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)からのみ、キャッシュの推奨事項が提供されます。 これらは過去 30 日間における実際のクエリのルックバック ウィンドウと、少なくとも 95% のクエリに対して最適化されていないキャッシュ ポリシーに基づいています。 

Azure Advisor のキャッシュ削減の推奨事項は、"データの制限がある" クラスターで利用できます。 これは、CPU とインジェストの使用率が低いにもかかわらず、データ容量が大きいために、スケールインまたはスケールダウンできなかったクラスターを意味します。

[![キャッシュの詳細のスクリーンショット。](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

### <a name="cluster-boundaries-tab"></a>[Cluster Boundaries]\(クラスターの境界\) タブ

**[Cluster Boundaries]\(クラスターの境界\)** タブには、使用状況に基づいたクラスターの境界が表示されます。 このタブでは、CPU、インジェスト、キャッシュの使用状況を調べることができます。 これらのメトリックは、**低**、**中** または **高** としてスコア付けされます。 これらのメトリックとスコアは、クラスターに最適な SKU とインスタンス数を決定する際に重要です。 Azure Advisor の SKU/サイズに関する推奨事項で考慮に入れられます。 

このタブでは、メトリック タイルを選択して詳細を確認し、その傾向およびスコアがどのように決定されたかを把握することができます。 また、クラスターに対する Azure Advisor の SKU/サイズに関する推奨事項も表示できます。 たとえば、次の図では、すべてのメトリックが **低** としてスコア付けされていることがわかります。 クラスターには、スケールインまたはスケールダウンしてコストを削減できるようにする、コストに関する推奨事項が提示されます。

> [!div class="mx-imgBorder"]
> [![[Cluster Boundaries]\(クラスターの境界\) のスクリーンショット。](./media/data-explorer/cluster-boundaries.png)](./media/data-explorer/cluster-boundaries.png#lightbox)

## <a name="pin-to-an-azure-dashboard"></a>Azure ダッシュボードにピン留めする

セクションの右上にある画びょうアイコンを選択することで、("大規模な" 分析観点の) メトリック セクションのいずれかを Azure ダッシュボードにピン留めできます。

![ピン留めアイコンが選択されているスクリーンショット。](./media/data-explorer/pin.png)

## <a name="customize-azure-data-explorer-insights"></a>Azure Data Explorer Insights のカスタマイズ

ブックは、データ分析のニーズに合わせて編集し、カスタマイズすることができます。
* 特定のサブスクリプションまたは Azure Data Explorer クラスターが常に選択されるようにブックのスコープを設定する。
* グリッドでメトリックを変更する。
* しきい値、カラー レンダリング、色分けを変更する。

カスタマイズを開始するには、上部のツールバーの **[カスタマイズ]** ボタンを選択します。

![[カスタマイズ] ボタンのスクリーンショット。](./media/data-explorer/customize.png)

カスタマイズはカスタム ブックに保存されるため、発行されたブックで既定の構成が上書きされることはありません。 ブックは、リソース グループ内の、ユーザー個人の **[個人用レポート]** セクション、またはリソース グループにアクセスできるユーザーであればだれでもアクセスできる **[共有レポート]** セクションのいずれかに保存されます。 カスタム ブックを保存した後は、ブック ギャラリーに移動して開きます。

![ブック ギャラリーのスクリーンショット。](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>トラブルシューティング

一般的なトラブルシューティングのガイダンスについては、「[ブックベースの分析情報のトラブルシューティング](troubleshoot-workbooks.md)」の記事を参照してください。

以降のセクションでは、Azure Data Explorer Insights を使用する際に遭遇する可能性のある一般的な問題のいくつかを診断およびトラブルシューティングする方法について説明します。

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>サブスクリプション ピッカーに自分のすべてのサブスクリプションが表示されないのはなぜですか。

Azure Data Explorer Insights で表示されるのは、サブスクリプション フィルターの選択によって抽出された Azure Data Explorer クラスターを含むサブスクリプションだけです。 サブスクリプション フィルターは、Azure portal の **[ディレクトリ + サブスクリプション]** で選択します。

![サブスクリプション フィルターの選択のスクリーンショット。](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-dont-i-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-section"></a>使用状況、テーブル、またはキャッシュ セクションに Azure Data Explorer クラスターのデータが表示されないのはなぜですか。

ログベースのデータを表示するには、監視する Azure Data Explorer クラスターごとに[診断ログを有効にする](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs)必要があります。 これは、各クラスターの診断設定で行うことができます。 Log Analytics ワークスペースにデータを送信する必要があります。 有効にする必要がある診断ログは、**Command**、**Query**、**TableDetails**、**TableUsageStatistics** です。

### <a name="ive-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Azure Data Explorer クラスターのログは既に有効にしました。 それなのに、[Commands and Queries]\(コマンドとクエリ\) でデータを確認できないのはなぜですか。

現在、診断ログは、過去にさかのぼって機能することができません。 データが表示されるようになるのは、Azure Data Explorer に対してアクションが実行された後になります。 Azure Data Explorer クラスターがどの程度アクティブかに応じて、数時間から 1 日かかることがあります。

## <a name="next-steps"></a>次のステップ

[Azure Monitor ブックを使用した対話型レポートの作成](../visualize/workbooks-overview.md)に関するページを確認して、ブックがサポートするように設計されているシナリオ、新規レポートの作成方法と既存レポートのカスタマイズ方法などについて学習してください。
