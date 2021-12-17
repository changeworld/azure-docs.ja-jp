---
title: Azure HDInsight 用に Log Analytics データを移行する
description: Azure Monitor 統合に対する変更と、新しいテーブルを使用するためのベスト プラクティスについて説明します。
ms.service: hdinsight
ms.topic: how-to
ms.author: ali
author: AliciaLiMicrosoft
ms.date: 04/19/2021
ms.openlocfilehash: 823fb43afbd7ac51cafeff7e9e18472fe32c9aec
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742265"
---
# <a name="log-analytics-migration-guide-for-azure-hdinsight-clusters"></a>Azure HDInsight クラスター用の Log Analytics 移行ガイド

 Azure HDInsight は、エンタープライズ対応のマネージド クラスター サービスです。 このサービスにより、Azure で Apache Spark、Hadoop、HBase、Kafka などのオープンソースの分析フレームワークが実行されます。 Azure HDInsight は他の Azure サービスと統合されており、これにより、顧客はビッグ データ分析アプリケーションをより適切に管理できます。

Log Analytics では、ログ クエリの編集と実行を行うための Azure portal のツールが提供されます。 クエリは Azure Monitor ログによって収集されたデータから取得され、その結果が対話形式で分析されます。 顧客は、Log Analytics クエリを使用して、特定の条件に一致するレコードを取得できます。 また、クエリを使用して、傾向を特定したり、パターンを分析したり、データの分析情報を提示したりできます。

Azure HDInsight は、2017 年に Log Analytics との統合が可能になりました。 HDInsight の顧客は、HDInsight クラスターを監視し、クラスター内のログに対してクエリを実行するために、この機能を迅速に採用しています。 この機能の導入が増加する一方で、統合に関する顧客からのフィードバックが提供されています。

- 顧客は格納すべきログを判断できず、すべてのログを格納すると、コストが高くなる場合がある。
- 現在の HDInsight スキーマ ログは一貫性のある名前付け規則に従っておらず、一部のテーブルは反復的である。
- 顧客は、HDInsight クラスターの KPI を簡単に監視できるよう、既定のダッシュボードが必要である。
- 顧客は、単純なクエリを実行するために Log Analytics に移動する必要がある。

## <a name="solution-overview"></a>ソリューションの概要

Azure HDInsight チームは、顧客からのフィードバックを考慮して、Azure Monitor との統合に投資しています。 この統合により、以下が実現されます。

- 顧客の Log Analytics ワークスペース内の新しいテーブル セット。 新しいテーブルは、新しい Log Analytics パイプラインを介して配信されます。
- 高い信頼性
- 迅速なログの配信
- リソースベースのテーブルのグループ化と既定のクエリ



> [!NOTE]  
> 新しい Azure Montitor 統合は、パブリック プレビュー段階にあります。 これは、米国東部リージョンと西ヨーロッパ リージョンでのみ利用できます。


## <a name="benefits-of-the-new-azure-monitor-integration"></a>新しい Azure Monitor 統合のベネフィット

このドキュメントでは、Azure Monitor 統合に加えられた変更について説明し、新しいテーブルを使用するためのベスト プラクティスを示します。

**再設計されたスキーマ**: 新しい Azure Monitor 統合のスキーマ フォーマットがより適切に編成され、理解しやすくなっています。 従来のスキーマ内のあいまいさを可能な限り解消するためのスキーマが 3 分の 2 少なくなっています。

**選択的ログ (近日リリース)** : Log Analytics で使用できるログとメトリックが用意されています。 監視コストを節約するために、新しい選択的ログ機能がリリースされます。 この機能を使用して、さまざまなログとメトリック ソースをオンまたはオフにします。 この機能では、使用した分だけを支払います。

**クラスター ポータル統合のログ**: HDInsight クラスター ポータルには、 **[ログ]** ウィンドウが新しく追加されています。 クラスターへのアクセス権を持つユーザーは、このウィンドウにアクセスして、クラスター リソースによるレコードの送信先であるテーブルに対してクエリを実行できます。 ユーザーは、特定のクラスター リソースのレコードを表示するために、Log Analytics ワークスペースにアクセスする必要がなくなります。

**分析情報クラスター ポータル統合**: HDInsight クラスター ポータルには、 **[分析情報]** ウィンドウも新しく追加されています。 新しい Azure Monitor 統合を有効にした後、 **[分析情報]** ウィンドウを選択すると、クラスターの種類に固有の既定のログおよびメトリック ダッシュボードが自動的に設定されます。 これらのダッシュボードは、以前の Azure ソリューションから改良されています。 これにより、クラスターのパフォーマンスと正常性に関する詳細な分析情報を得ることができます。

**大規模な分析情報**: **Azure Monitor** ポータルの新しい **大規模な分析情報** ブックを使用して、さまざまなサブスクリプション間でクラスターの正常性とパフォーマンスを監視できます。

## <a name="customer-scenarios"></a>顧客シナリオ

次のセクションでは、さまざまなシナリオで新しい Azure Monitor 統合を使用する方法について説明します。 「[新しい Azure Monitor 統合をアクティブ化する](#activate-a-new-azure-monitor-integration)」セクションでは、新しい Azure Monitor 統合をアクティブ化して使用する方法について説明します。 [Azure Monitor クラシックから新しい Azure Monitor 統合への移行](#migrate-to-the-new-azure-monitor-integration)に関するセクションには、以前の Azure Monitor 統合に依存するユーザー向けの追加情報が記載されています。

> [!NOTE]
> 新しい Azure 監視統合の対象となるのは、2020 年 9 月下旬に作成されたクラスターだけです。

## <a name="activate-a-new-azure-monitor-integration"></a>新しい Azure Monitor 統合をアクティブ化する 

> [!NOTE]
> 新しい統合を有効にする前に、アクセスできるサブスクリプションに Log Analytics ワークスペースを作成しておく必要があります。 Log Analytics ワークスペースの作成方法の詳細については、「[Azure portal で Log Analytics ワークスペースを作成する](../azure-monitor/logs/quick-create-workspace.md)」を参照してください。

クラスターのポータル ページに移動し、 **[監視]** セクションに達するまで左側のメニューを下にスクロールして、新しい統合をアクティブ化します。 **[監視]** セクションで、 **[Monitor integration]\(Monitor 統合\)** を選択します。 次に、 **[有効]** を選択すると、ログの送信先となる Log Analytics ワークスペースを選択できます。 ワークスペースを選択したら、 **[保存]** を選択します。 

### <a name="access-the-new-tables"></a>新しいテーブルにアクセスする

新しいテーブルにアクセスするには、2 つの方法があります。 

### <a name="approach-1"></a>アプローチ 1: 
新しいテーブルにアクセスする 1 つ目の方法では、Log Analytics ワークスペースを使用します。 

1. 統合を有効にしたときに選択した Log Analytics ワークスペースにアクセスします。 
2. 画面の左側にあるメニューを下にスクロールし、 **[ログ]** を選択します。 ログ クエリ エディターに、ワークスペース内のすべてのテーブルの一覧が表示されます。 
3. テーブルを **[ソリューション]** 別にグループ化している場合、新しい HDI テーブルは **[Logs Management]\(ログ管理\)** セクションに表示されます。 
4. テーブルを **[リソースの種類]** 別にグループ化すると、次の図に示すように、テーブルは **[HDInsight クラスター]** セクションに表示されます。 

> [!NOTE]
> このプロセスは、以前の統合でのログへのアクセス方法を示しています。 これには、ユーザーがワークスペースにアクセスできる必要があります。

### <a name="approach-2"></a>アプローチ 2: 

新しいテーブルにアクセスする 2 つ目の方法では、クラスター ポータル アクセスを使用します。
 
1. クラスターのポータル ページに移動し、 **[監視]** セクションが表示されるまで左側のメニューを下にスクロールします。 このセクションには、 **[ログ]** ウィンドウが表示されます。 
2. **[ログ]** を選択すると、ログ クエリ エディターが表示されます。 このエディターには、クラスター リソースに関連付けられているすべてのログが含まれています。 統合を有効にしたときに、Log Analytics ワークスペースにログが送信されています。 これらのログにより、リソースベースのアクセス (RBAC) が実現されます。 RBAC では、クラスターにアクセスできるがワークスペースへのアクセス権を持たないユーザーは、クラスターに関連付けられているログを確認できます。

比較のために、次のスクリーンショットは、従来の統合ワークスペース ビューと新しい統合ワークスペース ビューを示しています。

**従来の統合ワークスペース ビュー**

  :::image type="content" source="./media/log-analytics-migration/legacy-integration-workspace-view.png"  lightbox="./media/log-analytics-migration/legacy-integration-workspace-view.png" alt-text="従来の統合ワークスペース ビューを示すスクリーンショット。" border="false":::

**新しい統合ワークスペース ビュー**

  :::image type="content" source="./media/log-analytics-migration/new-integration-workspace-view.png" lightbox="./media/log-analytics-migration/new-integration-workspace-view.png" alt-text="新しい統合ワークスペース ビューを示すスクリーンショット。" border="false":::

### <a name="use-the-new-tables"></a>新しいテーブルを使用する

これらの統合により、新しいテーブルを使用することができます。

#### <a name="default-queries-to-use-with-new-tables"></a>新しいテーブルで使用する既定のクエリ

ログ クエリ エディターで、テーブルの一覧の上にある **[クエリ]** への切り替えを設定します。 **[リソースの種類]** 別にクエリをグループ化し、 **[HDInsight クラスター]** 以外のリソースの種類に対してフィルターが設定されていないことをご確認ください。 次の図は、 **[リソースの種類]** 別にグループ化され、 **[HDInsight クラスター]** に対してフィルター処理された場合の結果の外観を示しています。 いずれか 1 つを選択すると、ログ クエリ エディターに表示されます。 クエリに含まれているコメントを必ず読んでください。クエリを正常に実行するために、クラスター名などの情報を入力しなければならない場合があります。

:::image type="content" source="./media/log-analytics-migration/default-query-results-grouped-resource-type.png" alt-text="リソースの種類別にグループ化された既定のクエリ結果を示すスクリーンショット。" border="true":::


#### <a name="create-your-own-queries"></a>独自のクエリを作成する

ログ クエリ エディターで独自のクエリを入力できます。 古いテーブルで使用されるクエリは、新しいテーブルでは有効になりません。新しいテーブルの多くには、改良された新しいスキーマが含まれるためです。 新しいテーブルでクエリを構成するときに、既定のクエリを参照すると役立ちます。

#### <a name="insights"></a>洞察

分析情報は、[Azure ブック](../azure-monitor/visualize/workbooks-overview.md)を使用して作成されたクラスター固有の視覚エフェクト ダッシュボードです。 これらのダッシュボードを使用すると、クラスターがどのように実行されているかを示す詳細なグラフや視覚エフェクトが得られます。 これらのダッシュボードには、クラスターの種類、YARN、システム メトリック、およびコンポーネント ログごとにセクションがあります。 クラスターのダッシュボードにアクセスするには、ポータルでクラスターのページにアクセスし、 **[監視]** セクションまで下にスクロールして、 **[分析情報]** ウィンドウを選択します。 新しい統合を有効にした場合、ダッシュボードは自動的に読み込まれます。 ログのクエリを実行すると、グラフが読み込まれるまで数秒かかります。

:::image type="content" source="./media/log-analytics-migration/visualization-dashboard.png" lightbox="./media/log-analytics-migration/visualization-dashboard.png" alt-text="視覚エフェクト ダッシュボードを示すスクリーンショット。":::

#### <a name="custom-azure-workbooks"></a>カスタム Azure ブック

カスタムのグラフと視覚エフェクトを使用して、独自の Azure ブックを作成できます。 クラスターのポータル ページで、 **[監視]** セクションまで下にスクロールし、左側のメニューで **[ブック]** ウィンドウを選択します。 空のテンプレートを使用するか、 **[HDInsight クラスター]** セクションのいずれかのテンプレートを使用することができます。 クラスターの種類ごとにテンプレートがあります。 テンプレートは、既定の HDInsight 分析情報では提供されない特定のカスタマイズを保存する場合に便利です。 必要なものがないと思われる場合は、HDInsight 分析情報の新機能に関する要求をお気軽に送信してください。

#### <a name="at-scale-workbooks-for-new-azure-monitor-integrations"></a>新しい Azure Monitor 統合用の大規模なブック

新しい大規模なブックを使用して、クラスターのマルチクラスター監視エクスペリエンスを実現してください。 当社の大規模なブックには、監視パイプラインが有効になっているクラスターが表示されます。 また、このブックでは、複数のクラスターの正常性を一度に確認する簡単な方法が提供されます。 このブックを表示するには、次の手順に従います。

1. Azure portal ホーム ページから **[Azure Monitor]** ページにアクセスします。
2. **[Azure Monitor]** ページで、 **[分析情報]** セクションの **[Insights Hub]\(分析情報ハブ\)** を選択します。
3. **[分析]** セクションで、 **[HDInsight クラスター]** を選択します。

   :::image type="content" source="./media/log-analytics-migration/at-scale-workbook.png" lightbox="./media/log-analytics-migration/at-scale-workbook.png" alt-text="大規模なブックを示すスクリーンショット。" border="false":::

#### <a name="alerts"></a>警告

ログ クエリ エディターで、クラスターおよびワークスペースにカスタム アラートを追加できます。 クラスターまたはワークスペース ポータルから **[ログ]** ウィンドウを選択して、ログ クエリ エディターにアクセスします。 次のスクリーンショットに示すように、クエリを実行し、 **[新しいアラート ルール]** を選択します。 詳細については、[アラートの構成](../azure-monitor/alerts/alerts-log.md)に関するページを参照してください。

:::image type="content" source="./media/log-analytics-migration/new-rule-alert.png" alt-text="新しいルール アラートを示すスクリーンショット" border="false":::。

## <a name="migrate-to-the-new-azure-monitor-integration"></a>新しい Azure Monitor 統合に移行する

クラシック Azure Monitor 統合を使用している場合は、新しい Azure Monitor 統合に切り替えた後に、新しいテーブル形式に対していくつかの調整を行う必要があります。

新しい Azure Monitor 統合を有効にするには、「[新しい Azure Monitor 統合をアクティブ化する](#activate-a-new-azure-monitor-integration)」セクションで説明されている手順に従います。

### <a name="run-queries-in-log-analytics"></a>Log Analytics でクエリを実行する

新しいテーブル形式は前の形式とは異なるため、新しいテーブルを使用できるようにクエリを変更する必要があります。 新しい Azure Monitor 統合を有効にすると、テーブルとスキーマを参照して、古いクエリで使用されているフィールドを特定できます。

ダッシュボードとクエリの移行に必要な新しいフィールドを簡単に見つけることができるように、古いテーブルから新しいテーブルへの[マッピング テーブル](#appendix-table-mapping)が提供されています。

**既定のクエリ**: 一般的な状況で新しいテーブルを使用する方法を示す既定のクエリが作成されています。 既定のクエリでは、各テーブルで使用できる情報も表示されます。 既定のクエリにアクセスするには、この記事の「[新しいテーブルで使用する既定のクエリ](#default-queries-to-use-with-new-tables)」セクションの手順に従ってください。

### <a name="update-dashboards-for-hdinsight-clusters"></a>HDInsight クラスターのダッシュボードを更新する

HDInsight クラスターを監視するために複数のダッシュボードを作成した場合は、新しい Azure Monitor 統合を有効にした後、テーブルの背後にあるクエリを調整する必要があります。 新しい統合ではテーブル名またはフィールド名が変更される可能性がありますが、以前の統合に含まれるすべての情報が含まれています。

ダッシュボードの背後にあるクエリを更新するには、古いテーブルまたはスキーマから新しいテーブルまたはスキーマへの[マッピング テーブル](#appendix-table-mapping)を参照してください。

#### <a name="out-of-box-dashboards"></a>既定のダッシュボード 

また、既定のダッシュボードがクラスターレベルで改善されました。 各グラフの右上には、情報を生成する基になるクエリを表示するためのボタンがあります。 グラフにより、新しいテーブルに対して効果的にクエリを実行する方法を理解することができます。 既定のダッシュボードにアクセスするには、「[分析情報](#insights)」セクションと「[新しい Azure Monitor 統合用の大規模なブック](#at-scale-workbooks-for-new-azure-monitor-integrations)」セクションの手順に従ってください。

### <a name="use-an-hdinsight-at-scale-monitoring-dashboard"></a>HDInsight の大規模な監視ダッシュボードを使用する

HDInsight Spark Monitoring や HDInsight Interactive Monitoring など、HDInsight クラスター用の既定の監視ダッシュボードを使用しているユーザー向けに、Azure Monitor ポータルでも同じ機能が提供される予定です。

Azure Monitor には HDInsight クラスター オプションがあります。

   :::image type="content" source="./media/log-analytics-migration/hdinsight-azure-monitor.png" lightbox="./media/log-analytics-migration/hdinsight-azure-monitor.png" alt-text="Azure Monitor の HDInsight オプションを示すスクリーンショット。" border="false":::

Azure Monitor ポータルの [Insights Hub]\(分析情報ハブ)\ では、複数の HDInsight クラスターを 1 か所で監視する機能が提供されています。 ワークロードの種類に基づいてクラスターが編成されているため、Spark、HBase、Hive などの種類が表示されます。 複数のダッシュボードに移動するのではなく、このビューですべての HDInsight クラスターを監視できるようになりました。

> [!NOTE]
> 詳細については、この記事の「[分析情報](#insights)」セクションと「[新しい Azure Monitor 統合用の大規模なブック](#at-scale-workbooks-for-new-azure-monitor-integrations)」セクションを参照してください。

## <a name="enable-both-integrations-to-accelerate-the-migration"></a>移行を加速させるために両方の統合を有効にする

新しい Azure Monitor 統合に迅速に移行するために、両方の統合の対象となるクラスターで、クラシックと新しい Azure Monitor 統合の両方を同時にアクティブ化することができます。 新しい統合は、2020 年 9 月中旬以降に作成されたすべてのクラスターで使用できます。

これにより、使用しているクエリの並列比較を簡単に行うことができます。

### <a name="enabling-the-classic-integration"></a>クラシック統合を有効にする

2020 年 9 月中旬以降に作成されたクラスターを使用している場合は、クラスターのポータルに新しいポータル エクスペリエンスが表示されます。 新しいパイプラインを有効にするには、「[新しい Azure Monitor 統合をアクティブ化する](#activate-a-new-azure-monitor-integration)」セクションで説明されている手順に従います。 このクラスターでクラシック統合をアクティブ化するには、クラスターのポータル ページにアクセスします。 クラスター ポータル ページの左側にあるメニューの **[監視]** セクションで、 **[Monitor integration]\(Monitor 統合\)** ウィンドウを選択します。 **[Configure Azure Monitor for HDInsight clusters integration (classic)]\(HDInsight クラスター統合 (クラシック) 用に Azure Monitor を構成する\)** を選択します。 サイド コンテキストが表示され、クラシック Azure 監視統合を有効または無効にすることができます。 

> [!NOTE]
> クラスター ポータルのログと分析情報のページでは、クラシック統合のログやメトリックは表示されません。 これらの場所には、新しい統合のログとメトリックのみが表示されます。

   :::image type="content" source="./media/log-analytics-migration/hdinsight-classic-integration.png" alt-text="クラシック統合にアクセスするためのリンクを示すスクリーンショット。" border="false":::

2021 年 7 月 1 日以降、クラシック Azure Monitor 統合を使用して新しいクラスターを作成することはできなくなります。

## <a name="release-and-support-timeline"></a>リリースとサポートのタイムライン

- 2021 年 10 月 15 日以降、クラシック Azure 監視統合は利用できなくなります。 その日付以降は、クラシック Azure 監視統合を有効にできません。
- 既存のクラシック Azure 監視統合は引き続き機能します。 クラシック Azure 監視統合のサポートには制約があります。 
  - 顧客がサポート チケットを送信すると、問題が調査されます。
  - ソリューションにイメージの変更が必要な場合、顧客は新しい統合に移行する必要があります。
  - 重要なセキュリティの問題を除き、クラシック Azure 監視統合クラスターには修正プログラムは適用されません。

## <a name="appendix-table-mapping"></a>付録: テーブル マッピング

次のグラフは、クラシック Azure 監視統合から新しい統合へのテーブル マッピングを示しています。 "**ワークロード**" 列は、各テーブルが関連付けられているワークロードを示しています。 "**新しいテーブル**" 行は、新しいテーブルの名前を示しています。 "**説明**" 行では、このテーブルで使用できるログまたはメトリックの種類について説明しています。 "**古いテーブル**" 行は、"**新しいテーブル**" 行に示されているテーブル内にそのデータが含まれるようになった、クラシック Azure Monitor 統合のすべてのテーブルの一覧です。

> [!NOTE]
> 古いテーブルに基づいていない新しいテーブルもあります。

## <a name="general-workload-tables"></a>一般的なワークロード テーブル

| 新しいテーブル | 詳細 |
| --- | --- |
| HDInsightAmbariSystemMetrics | <ul><li>**説明**: このテーブルには、Ambari から収集されたシステム メトリックが含まれています。 これらのメトリックは、2 つのヘッドノードだけではなく、クラスター内の各ノード (エッジ ノードを除く) から取得されるようになりました。 各メトリックは列になり、各メトリックはレコードごとに 1 回報告されます。</li><li>**古いテーブル**: metrics\_cpu\_nice\_cl、metrics\_cpu\_system\_cl、metrics\_cpu\_user\_cl、metrics\_memory\_cache\_CL、metrics\_memory\_swap\_CL、metrics\_memory\_total\_CLmetrics\_memory\_buffer\_CL、metrics\_load\_1min\_CL、metrics\_load\_cpu\_CL、metrics\_load\_nodes\_CL、metrics\_load\_procs\_CL、metrics\_network\_in\_CL、metrics\_network\_out\_CL</li></ul>|
| HDInsightAmbariClusterAlerts | <ul><li>**説明**: このテーブルには、クラスター内の各ノードからの Ambari クラスター アラート (エッジ ノードを除く) が含まれています。 各アラートは、このテーブル内のレコードです。</li><li>**古いテーブル**: metrics\_cluster\_alerts\_CL</li></ul>|
| HDInsightSecurityLogs | <ul><li>**説明**: このテーブルには、Ambari 監査ログおよび認証ログのレコードが含まれています。</li><li>**古いテーブル**: log\_ambari\_audit\_CL、log\_auth\_CL</li></ul>|
| HDInsightRangerAuditLogs | <ul><li>**説明**: このテーブルには、ESP クラスターの Ranger 監査ログのすべてのレコードが含まれています。</li><li>**古いテーブル**: ranger\_audit\_logs\_CL</li></ul>|
| HDInsightGatewayAuditLogs\_CL | <ul><li>**説明**: このテーブルには、Gateway ノードの監査情報が含まれています。 これは、"古いテーブル" 列のテーブルと同じ形式です。 **これは、引き続きカスタム ログ セクションにあります。**</li><li>**古いテーブル**: log\_gateway\_Audit\_CL</li></ul>|

## <a name="spark-workload"></a>Spark ワークロード

> [!NOTE]
> Spark アプリケーションに関連するテーブルは、Spark ワークロードに関するより詳細な情報を提供する 11 個の新しい Spark テーブル (HDInsightSpark* 以降) に置き換えられました。


| 新しいテーブル | 詳細 |
| --- | --- |
| HDInsightSparkLogs | <ul><li>**説明**: このテーブルには、Spark とそれに関連するコンポーネント (Livy と Jupyter) に関連するすべてのログが含まれています。</li><li>**古いテーブル**: log\_livy、\_CL、log\_jupyter\_CL、log\_spark\_CL、log\_sparkappsexecutors\_CL、log\_sparkappsdrivers\_CL</li></ul>|
| HDInsightSparkApplicationEvents | <ul><li>**説明**: このテーブルには、送信時刻と完了時刻、アプリ ID、AppName などの Spark アプリケーションのイベント情報が含まれています。 これは、アプリケーションの開始時刻と完了時刻を追跡するのに役立ちます。 </li></ul>|
| HDInsightSparkBlockManagerEvents | <ul><li>**説明**: このテーブルには、Spark のブロック マネージャーに関連するイベント情報が含まれています。 これには、実行プログラムのメモリ使用量などの情報が含まれます。</li></ul>|
| HDInsightSparkEnvironmentEvents | <ul><li>**説明**: このテーブルには、アプリケーションが実行される環境に関連するイベント情報が含まれています。これには、Spark デプロイ モード、マスター、および実行プログラムに関する情報が含まれます。</li></ul>|
| HDInsightSparkExecutorEvents | <ul><li>**説明**: このテーブルには、アプリケーションによる Spark 実行プログラムの使用状況に関するイベント情報が含まれています。</li></ul>|
| HDInsightSparkExtraEvents | <ul><li>**説明**: このテーブルには、他の Spark テーブルに収まらないイベント情報が含まれています。 </li></ul>|
| HDInsightSparkJobEvents | <ul><li>**説明**: このテーブルには、開始時刻と終了時刻、結果、関連するステージなど、Spark ジョブに関する情報が含まれています。</li></ul>|
| HDInsightSparkSqlExecutionEvents | <ul><li>**説明**: このテーブルには、プラン情報と説明、開始時刻と終了時刻など、Spark SQL クエリに関するイベント情報が含まれています。</li></ul>|
| HDInsightSparkStageEvents | <ul><li>**説明**: このテーブルには、開始時刻と完了時刻、エラーの状態、詳細な実行情報など、Spark ステージのイベント情報が含まれています。</li></ul>|
| HDInsightSparkStageTaskAccumulables | <ul><li>**説明**: このテーブルには、ステージとタスクのパフォーマンス メトリックが含まれています。</li></ul>|
| HDInsightTaskEvents | <ul><li>**説明**: このテーブルには、開始時刻と終了時刻、関連付けられているステージ、実行状態、タスクの種類など、Spark タスクのイベント情報が含まれています。</li></ul>|
| HDInsightJupyterNotebookEvents | <ul><li>**説明**: このテーブルには、Jupyter ブックのイベント情報が含まれています。</li></ul>|

## <a name="hadoopyarn-workload"></a>Hadoop または YARN ワークロード

| 新しいテーブル | 詳細 |
| --- | --- |
| HDInsightHadoopAndYarnMetrics | <ul><li>**説明**: このテーブルには、Hadoop フレームワークと YARN フレームワークの JMX メトリックが含まれています。 これには、古いカスタム ログ テーブルと同じすべての JMX メトリックに加えて、重要と考えられるメトリックが含まれています。 タイムライン サーバー、ノード マネージャー、およびジョブ履歴サーバーのメトリックが追加されました。 レコードごとに 1 つのメトリックが含まれています。</li><li>**古いテーブル**: metrics\_resourcemanager\_clustermetrics\_CL、metrics\_resourcemanager\_jvm\_CL、metrics\_resourcemanager\_queue\_root\_CL、metrics\_resourcemanager\_queue\_root\_joblauncher\_CL、metrics\_resourcemanager\_queue\_root\_default\_CL、metrics\_resourcemanager\_queue\_root\_thriftsvr\_CL</li></ul>|
| HDInsightHadoopAndYarnLogs | <ul><li>**説明**: このテーブルには、Hadoop フレームワークと YARN フレームワークから生成されたすべてのログが含まれています。</li><li>**古いテーブル**: log\_mrjobsummary\_CL、log\_resourcemanager\_CL、log\_timelineserver\_CL、log\_nodemanager\_CL</li></ul>|

 
## <a name="hivellap-workload"></a>Hive または LLAP ワークロード 

| 新しいテーブル | 詳細 |
| --- | --- |
| HDInsightHiveAndLLAPMetrics | <ul><li>**説明**: このテーブルには、Hive フレームワークと LLAP フレームワークの JMX メトリックが含まれています。 これには、古いカスタム ログ テーブルと同じすべての JMX メトリックが含まれます。 レコードごとに 1 つのメトリックが含まれています。</li><li>**古いテーブル**: llap\_metrics\_hiveserver2\_CL、llap\_metrics\_hs2\_metrics\_subsystemllap\_metrics\_jvm\_CL、llap\_metrics\_llap\_daemon\_info\_CL、llap\_metrics\_buddy\_allocator\_info\_CL、llap\_metrics\_deamon\_jvm\_CL、llap\_metrics\_io\_CL、llap\_metrics\_executor\_metrics\_CL、llap\_metrics\_metricssystem\_stats\_CL、llap\_metrics\_cache\_CL</li></ul>|
| HDInsightHiveAndLLAPLogs | <ul><li>**説明**: このテーブルには、Hive、LLAP、およびそれらに関連するコンポーネント (WebHCat と Zeppelin) から生成されたログが含まれています。</li><li>**古いテーブル**: log\_hivemetastore\_CL log\_hiveserver2\_CL、log\_hiveserve2interactive\_CL、log\_webhcat\_CL、log\_zeppelin\_zeppelin\_CL</li></ul>|


## <a name="kafka-workload"></a>Kafka ワークロード

| 新しいテーブル | 詳細 |
| --- | --- |
| HDInsightKafkaMetrics | <ul><li>**説明**: このテーブルには、Kafka の JMX メトリックが含まれています。 これには、古いカスタム ログ テーブルと同じすべての JMX メトリックに加えて、重要と考えられるメトリックが含まれています。 レコードごとに 1 つのメトリックが含まれています。</li><li>**古いテーブル**: metrics\_kafka\_CL</li></ul>|
| HDInsightKafkaLogs | <ul><li>**説明**: このテーブルには、Kafka ブローカーから生成されたすべてのログが含まれています。</li><li>**古いテーブル**: log\_kafkaserver\_CL、log\_kafkacontroller\_CL</li></ul>|

## <a name="hbase-workload"></a>HBase ワークロード

| 新しいテーブル | 詳細 |
| --- | --- |
| HDInsightHBaseMetrics | <ul><li>**説明**: このテーブルには、HBase の JMX メトリックが含まれています。 これには、"古いスキーマ" 列に示されているテーブルと同じすべての JMX メトリックが含まれます。 古いテーブルとは異なり、各行に 1 つのメトリックが含まれています。</li><li>**古いテーブル**: metrics\_regionserver\_CL、metrics\_regionserver\_wal\_CL、metrics\_regionserver\_ipc\_CL、metrics\_regionserver\_os\_CL、metrics\_regionserver\_replication\_CL、metrics\_restserver\_CL、metrics\_restserver\_jvm\_CL、metrics\_hmaster\_assignmentmanager\_CL、metrics\_hmaster\_ipc\_CL、metrics\_hmaser\_os\_CL、metrics\_hmaster\_balancer\_CL、metrics\_hmaster\_jvm\_CL、metrics\_hmaster\_CL、metrics\_hmaster\_fs\_CL</li></ul>|
| HDInsightHBaseLogs | <ul><li>**説明**: このテーブルには、HBase とそれに関連するコンポーネント (Phoenix と HDFS) のログが含まれています。</li><li>**古いテーブル**: log\_regionserver\_CL、log\_restserver\_CL、log\_phoenixserver\_CL、log\_hmaster\_CL、log\_hdfsnamenode\_CL、log\_garbage\_collector\_CL</li></ul>|

## <a name="storm-workload"></a>Storm ワークロード

| 新しいテーブル | 詳細 |
| --- | --- |
| HDInsightStormMetrics | <ul><li>**説明**: このテーブルには、"古いテーブル" セクションのテーブルと同じ JMX メトリックが含まれています。 この行には、レコードごとに 1 つのメトリックが含まれています。</li><li>**古いテーブル**: metrics\_stormnimbus\_CL、metrics\_stormsupervisor\_CL</li></ul>|
| HDInsightStormTopologyMetrics | <ul><li>**説明**: このテーブルには、Storm のトポロジ レベル メトリックが含まれています。 これは、"古いテーブル" セクションに示されているテーブルと同じ形状です。</li><li>**古いテーブル**: metrics\_stormrest\_CL</li></ul>|
| HDInsightStormLogs | <ul><li>**説明**: このテーブルには、Storm から生成されたすべてのログが含まれています。</li><li>**古いテーブル**: log\_supervisor\_CL、log\_nimbus\_CL</li></ul>|

## <a name="oozie-workload"></a>Oozie ワークロード

| 新しいテーブル | 詳細 |
| --- | --- |
| HDInsightOozieLogs | <ul><li>**説明**: このテーブルには、Oozie フレームワークから生成されたすべてのログが含まれています。</li><li>**古いテーブル**: Log\_oozie\_CL</li></ul>|

## <a name="next-steps"></a>次のステップ
[Azure Monitor ログでクエリを実行して HDInsight クラスターを監視する](hdinsight-hadoop-oms-log-analytics-use-queries.md)
