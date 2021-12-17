---
title: Log Analytics Workspace Insights
description: Log Analytics Workspace Insights の概要 - インジェスト、使用量、正常性、エージェントなど
services: azure-monitor
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 05/06/2021
ms.openlocfilehash: c56a90ab18467e67360653c83d32c7cfc6f84078
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133255"
---
# <a name="log-analytics-workspace-insights"></a>Log Analytics Workspace Insights

Log Analytics Workspace Insights では、ワークスペースの使用量、パフォーマンス、正常性、エージェント、クエリ、変更ログの包括的な監視を行います。 この記事は、Log Analytics Workspace Insights をオンボードする方法を理解するのに役立ちます。

## <a name="overview-your-log-analytics-workspaces"></a>Log Analytics ワークスペースの概要

Azure Monitor Insights で Log Analytics Workspace Insights にアクセスすると、“大規模” 視点の画面が表示されます。 ここで、ワークスペースが世界に広がっている様子を見たり、データ保持期間や上限とライセンスの詳細 (色分け) を確認したり、ワークスペースを選んでインサイトを見たりできます。


:::image type="content" source="media/log-analytics-workspace-insights-overview/at-scale.png" alt-text="Log Analytics Workspace Insights に一覧表示されたワークスペースのスクリーンショット" lightbox="media/log-analytics-workspace-insights-overview/at-scale-expanded.png":::


Log Analytics Workspace Insights を大規模視点で開くには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. Azure portal の左側のペインで **[Monitor]** を選択し、[Insights Hub] セクションで **[Log Analytics Workspace Insights]** を選択します。

## <a name="view-insights-for-a-log-analytics-workspace"></a>Log Analytics ワークスペースのインサイトを見る

特定のワークスペースのインサイトを開くと、ワークスペースのパフォーマンス、使用量、正常性、エージェント、クエリ、変更ログの豊富なデータと分析結果が表示されます。


:::image type="content" source="media/log-analytics-workspace-insights-overview/at-resource.png" alt-text="Log Analytics Workspace Insights の概要画面のスクリーンショット" lightbox="media/log-analytics-workspace-insights-overview/at-resource.png":::

Log Analytics Workspace Insights にアクセスする方法

1. Azure Monitor で Open Log Analytics Workspace Insights を開きます (上で説明)

2. データを見るワークスペースを選びます

または 

1. Azure portal で **Log Analytics Workspaces** をクリックします

2. Log Analytics ワークスペースを選びます

3. [監視] の中にある [ワークスペース] で **[インサイト]** をクリックします

データはタブで整理されています。画面上部に表示される対象期間 (既定は 24 時間) が、すべてのタブに適用されます。 一部のグラフと表には、タイトルが示すとおり、異なる対象期間が適用されます。


### <a name="overview-tab"></a>[概要] タブ

**[概要]** タブでは次のものを見ることができます。

* 主要な統計情報と設定
    - ワークスペースの 1 カ月ごとのインジェストの分量
    - 指定期間中にハートビートを送信したマシン、つまり、このワークスペースに接続しているマシンの台数
    - 指定期間の最後の 1 時間にハートビートを送信していないマシン
    - データ保持期間のセット
    - 1 日ごとの上限のセットと、直近日において既にインジェストされたデータの量

* 上位 5 テーブル – 過去 1 カ月のインジェスト量が多かった上位 5 テーブルを分析するグラフ
    - 各テーブルにインジェストされたデータの分量
    - それぞれの 1 日ごとのインジェスト量 - 急増/急減を視覚的に表示
    - インジェストの異常 - これらのテーブルで見つかったインジェストの急増/急減のリスト


### <a name="usage-tab"></a>[使用] タブ

#### <a name="usage-dashboard"></a>[使用状況] ダッシュボード

このタブでは、ワークスペースの使用量の情報を見ることができます。 ダッシュボードのサブタブでは、テーブル別のインジェストのデータを見ることができます。既定では、指定期間中のインジェストが多い上位 5 テーブルを表示します ([概要] ページに表示されるテーブルと同じ)。 [ワークスペース テーブル] ドロップダウン メニューで、表示するテーブルを選べます。 

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage.png" alt-text="ワークスペースの [使用量] タブのスクリーンショット" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage.png":::

* メイン グリッド - ここで、ソリューションごとに分類されたテーブルと、各テーブルの情報を見ることができます。
    - 指定期間中にインジェストされたデータの量
    - 指定期間中のインジェスト量の全体のうち、このテーブルが占める割合。 インジェスト量への影響が大きいテーブルを把握するのに役立ちます。 下のスクリーンショットでは、 AzureDiagnostics と ContainerLog だけで、このワークスペースにインジェストされたデータの 2/3 (64%) を占めています。
    - 各テーブルの使用量の情報の最終更新時刻 - 使用量の情報は通常 1 時間ごとに更新します。 使用量の情報の更新は、サービス内部で定期的に実行する操作であり、そのデータの更新の遅延を通知することには、データの正確な解釈に必要な情報を提供する以上の目的はありません。 ユーザーによる対応は必要ありません。
    - 料金請求 - 料金を請求するテーブルとしないテーブルを示します。

* テーブル別の詳細情報

    ページ下部に、メイン グリッドで選択しているテーブルの詳細情報が表示されます。
    - インジェストの分量 - 各リソースからそのテーブルにインジェストされたデータの量と、その推移。 このテーブルに対するインジェスト全体の 30% 以上を占めるリソースには、目立つように警告マークが付されます。
    - インジェストの待機時間 - インジェストにかかった時間。テーブルに送信された要求について、50目、90目、95目の パーセンタイル値が示されます。 この領域の上部にあるグラフには、要求の合計インジェスト時間が表示されます。インジェスト時間は、操作の最初から最後まで、つまり、イベントが発生した時点から、ワークスペースへのインジェストが完了した時点までを指します。
    下部のグラフでは、エージェントの待機時間 (エージェントからワークスペースにログを送信するのにかかった時間) とパイプラインの待機時間 (サービスでデータを処理してワークスペースにプッシュするのにかかった時間) が別々に表示されます。
    :::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-ingestion-latency.png" alt-text="ワークスペースの [使用量] の [インジェスト待機時間] サブタブのスクリーンショット" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-ingestion-latency.png":::

#### <a name="additional-usage-queries"></a>その他の使用量クエリ

[その他のクエリ] サブタブでは、ワークスペースのすべてのテーブルで実行されるクエリの情報を確認できます (表示内容は、1 時間ごとに更新される使用量のメタデータに左右されません)。 対象となるクエリの分量が大変多く、効率的でもないため、自動では実行されません。 ただし、ワークスペースへのログ送信量が多いリソースに関する有益な情報が得られる場合があり、コスト削減にもつながるかもしれません。

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-additional-queries.png" alt-text="ワークスペースの [使用量] の [その他のクエリ] タブのスクリーンショット" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-additional-queries.png":::

それらのクエリの 1 つに、“このワークスペースへのログ送信量が多い Azure リソース” があります (上位 50 個を表示)。
ここに示すデモ ワークスペースでは、3 つの Kubernetes クラスターから、他のリソースの合計よりもはるかに多くのデータが送信されていることが、よく分かります。特にその中の 1 つがワークスペースに大きな負荷をかけています。

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-additional-query-run.png" alt-text="ワークスペースの [使用量] の [その他のクエリ] タブのスクリーンショット。その他のクエリのうち 1 種類の結果が表示されている。" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-additional-query-run.png":::


### <a name="health-tab"></a>[正常性] タブ

このタブには、ワークスペースの正常性状態、それを最後に報告した時刻、操作[エラーと警告](../logs/monitor-workspace.md) (_LogOperation テーブルから取得) が表示されます。 [ここ](../logs/monitor-workspace.md#categories)で一覧表示された問題と軽減手順に関する詳細を参照できます。

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-health.png" alt-text="ワークスペースの [正常性] タブのスクリーンショット" lightbox="media/log-analytics-workspace-insights-overview/workspace-health.png":::

### <a name="agents-tab"></a>[エージェント] タブ

このタブでは、このワークスペースにログを送信しているエージェントの情報を見ることができます。
:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-agents.png" alt-text="ワークスペースの [エージェント] タブのスクリーンショット" lightbox="media/log-analytics-workspace-insights-overview/workspace-agents.png":::

* 操作エラーと警告 - 特にエージェントに関連するエラーと警告です。 エラーと警告はタイトルごとにまとめられ、起こり得るさまざまな問題が見やすくなっていますが、正確な時刻とリソースを表示するよう展開することもできます。 また、“Logs でクエリを実行する” をクリックすると、Logs で _LogOperation テーブルに対するクエリを実行し、生データの確認やさらなる分析を行うことができます。
* ワークスペース エージェント - 指定期間中にワークスペースへログを送信したエージェントです。 エージェントの種類と正常性状態を確認できます。 “正常” と表記されているエージェントは、うまく機能しているとは限りません。これは単に、直近の 1 時間にハートビートを送信したことを表しています。 正常性状態の詳細は下部のグリッドに表示されます。
* エージェントの活動 - このグリッドには、すべてのエージェントの正常/異常の情報が表示されます。 ここでも、“正常” の表記は単に、直近の 1 時間にエージェントからハートビートが送信されたことを表しています。 詳しい状態を把握するには、グリッドに表示されるトレンドを確認します。このエージェントのハートビート送信数の推移が表示されています。 実際の正常性状態は、例えば、監視しているリソースの振る舞いを把握することではじめて推定できます。例として、コンピューターを特定の時刻に意図的にシャットダウンする場合、それに合わせて、エージェントのハートビートが断続的に止まっているように見えます。


### <a name="query-audit-tab"></a>[クエリ監査] タブ

クエリ監査では、ワークスペースで実行したクエリのログを作成します。 これを有効にした場合、このデータは、クエリのパフォーマンス、効率、負荷を理解し、改善するのに大いに役立ちます。 ワークスペースでクエリ監査を有効にしたい場合、またはそのことについて詳しく知りたい場合は、[Azure Monitor Logs による監査クエリ](../logs/query-audit.md)に関する記事をご覧ください。

#### <a name="performance"></a>パフォーマンス 
このタブでは、次のものを確認できます。
* クエリの所要時間 - 所要時間の 95目の パーセンタイル値と 50目の パーセンタイル値 (中間値) をミリ秒単位で、時間を追って表示します。
* 返された行の数 - 行数の 95目の パーセンタイル値と 50目の パーセンタイル値 (中間値) をミリ秒単位で、時間を追って表示します。
* データ処理量 - データ処理量の 95目の パーセンタイル値と 50目の パーセンタイル値、すべてのリクエストのデータ処理量の合計を、時間を追って表示します。
* 応答コード - 指定期間中のすべてのクエリに対する応答コードの分布。

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-query-audit-performance.png" alt-text="[クエリ監査] タブ、[パフォーマンス] サブタブ のスクリーンショット" lightbox="media/log-analytics-workspace-insights-overview/workspace-query-audit-performance.png":::

#### <a name="slow-and-inefficient-queries"></a>低速で非効率なクエリ 
このタブでは、見直しが必要かもしれない低速、非効率なクエリの把握に役立つ 2 つのグリッドを表示します。 これらのクエリは、ワークスペースに必要のない慢性的な負荷をかけるので、ダッシュボードと警告には使うべきでありません。
* リソース消費が大きいクエリ - CPU の使用量が大きい上位 10 種類のクエリです。データ処理量 (KB)、期間、クエリごとのテキストも合わせて表示されます。
* 最も低速クエリ - 上位 10 種類の最も低速なクエリで、データ処理量、期間、クエリごとのテキストも合わせて表示されます。

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-query-audit-slow-queries.png" alt-text="[クエリ監査] タブ、[低速クエリ] サブタブのスクリーンショット" lightbox="media/log-analytics-workspace-insights-overview/workspace-query-audit-slow-queries.png":::

#### <a name="query-users"></a>クエリ ユーザー 
このタブでは、このワークスペースに対するユーザーの操作を確認できます。
* ユーザーごとのクエリ - 指定期間中に各ユーザーが実行したクエリの回数
* 制限を受けたユーザー - ワークスペースに対するクエリが多すぎるため、クエリの実行に制限を受けたユーザー

### <a name="change-log-tab"></a>[変更ログ] タブ

このタブでは、(指定している期間にかかわらず) 過去 90 日間にワークスペースの構成に対して行った変更と、それを行ったユーザーを表示します。
データ処理量の上限やワークスペースのライセンスなど、ワークスペースの重要設定を変更するユーザーを監視することが目的です。

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-change-log.png" alt-text="ワークスペースの [変更ログ] タブのスクリーンショット" lightbox="media/log-analytics-workspace-insights-overview/workspace-change-log.png":::


## <a name="next-steps"></a>次のステップ

ブックがサポートするように設計されているシナリオ、新規レポートの作成方法と既存レポートのカスタマイズ方法などについては、「[Azure Monitor ブックを使用した対話型レポートの作成](../visualize/workbooks-overview.md)」で学習してください。
