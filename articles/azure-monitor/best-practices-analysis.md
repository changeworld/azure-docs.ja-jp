---
title: Azure Monitor のベスト プラクティス - 分析と視覚化
description: Azure Monitor の標準の分析機能の範囲を超えて視覚化をカスタマイズするためのガイダンスと推奨事項。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: c9f46404e1984c7f9eb4e8cea231229f31ffa8ad
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130231667"
---
# <a name="azure-monitor-best-practices---analyze-and-visualize-data"></a>Azure Monitor のベスト プラクティス - データの分析と視覚化
この記事は、[Azure Monitor を構成する際の推奨事項](best-practices.md)に関する記事で紹介されているシナリオの一部です。 ここでは、収集されたデータを分析するための Azure Monitor の組み込み機能と、組織のさまざまなユーザーの要件を満たすためにカスタムの視覚化を作成する場合のオプションについて説明します。 チャートやグラフなどで視覚化すると、監視データを分析して問題をドリルダウンし、パターンを特定しやすくなります。


## <a name="builtin-analysis-features"></a>組み込みの分析機能
以下のセクションでは、構成を行わずに、収集したデータの分析を行う Azure Monitor の機能について説明します。
### <a name="overview-page"></a>[概要] ページ
ほとんどの Azure サービスには Azure portal 内に **[概要]** ページがあり、重要なメトリックの最近のグラフを示す、グラフ付きの **[監視]** セクションが含まれています。 これは、個々のサービスの所有者がリソースのパフォーマンスをすばやく評価するためのものです。 このページは自動的に収集されるプラットフォーム メトリックに基づいているため、この機能に構成は必要ありません。

### <a name="metrics-explorer"></a>メトリックス エクスプローラー
メトリックス エクスプローラーを使用すると、ユーザーはメトリック データを対話的に操作し、メトリック アラートを作成できます。 ほとんどのユーザーは最小限のトレーニングでメトリックス エクスプローラーを使用できますが、分析するメトリックをよく理解していることが求められます。 データ収集を構成したら、この機能の構成は不要です。 Azure リソースのプラットフォーム メトリックが自動的に使用できるようになります。 仮想マシンのゲスト メトリックは Azure Monitor エージェントがデプロイされると使用できるようになり、アプリケーション メトリックは Application Insights が構成されると使用できるようになります。


### <a name="log-analytics"></a>Log Analytics
Log Analytics を使用すると、ユーザーはログ クエリを作成して、ログ データを対話的に操作し、ログ クエリ アラートを作成することができます。 ユーザーは、クエリ言語を理解できるようになるためにある程度のトレーニングを必要としますが、一般的な要件の場合は事前構築済みクエリを使用できます。 また、組織に固有のクエリを含む[クエリ パック](logs/query-packs.md)を追加することもできます。 これにより、クエリ言語に詳しいユーザーが、組織内の他のユーザーのためにクエリを作成できます。


## <a name="workbooks"></a>Workbooks
[ブック](./visualize/workbooks-overview.md)は、データ分析および豊富なビジュアル レポート作成のための柔軟なキャンバスを提供する、Azure 向けの最適な視覚化プラットフォームです。 ブックを使用すると、Azure 全体から複数のデータ ソースを活用し、それらを結合して、統合された対話型エクスペリエンスにすることができます。 これらは、複数の Azure リソースにわたって E2E 監視ビューを用意する場合に特に便利です。

分析情報では事前構築済みのブックが使用され、特定のサービスの正常性とパフォーマンスに関する重要な情報がユーザーに表示されます。 Azure Monitor メニューの **[ブック]** タブで追加のブックのギャラリーにアクセスし、さまざまなユーザーの要件を満たすカスタム ブックを作成できます。

![ページビューの分析、使用状況、ページで費やされた時間を含む、ブックからの 3 ページのスクリーンショットを示す図。](media/visualizations/workbook.png)

ブックの一般的なシナリオには次のようなものがあります。

- テーブル内の要素を選択することで関連するグラフと視覚化が動的に更新される、パラメーターを使用する対話型レポートを作成する。
- 組織内の他のユーザーとレポートを共有する。
- パブリック GitHub ベースのテンプレート ギャラリーを使用して、組織内の他のブック作成者と共同作業を行う。



## <a name="azure-dashboards"></a>Azure ダッシュボード
[Azure ダッシュボード](../azure-portal/azure-portal-dashboards.md)は、Azure インフラストラクチャとサービスに関する一元的なウィンドウを提供するのに役立ちます。 ブックにはより豊富な機能が用意されていますが、ダッシュボードでは、Azure Monitor のデータを他の Azure サービスのデータと組み合わせることができます。

![カスタマイズ可能な情報が表示されている Azure ダッシュボードの例を示すスクリーンショット。](media/visualizations/dashboard.png)

次のビデオ チュートリアルでは、ダッシュボードの作成について説明しています。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

ダッシュボードの一般的なシナリオには次のようなものがあります。

- メトリック グラフおよびログ クエリの結果を関連するサービスの運用データと組み合わせたダッシュボードを作成する。
- [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) との統合によってサービス所有者とダッシュボードを共有する。
  

Azure Monitor Logs のデータが含まれるダッシュボードを作成する方法の詳細については、「[Log Analytics データのダッシュボードを作成して共有する](visualize/tutorial-logs-dashboards.md)」を参照してください。 Application Insights のデータが含まれるダッシュボードを作成する方法の詳細については、「[Azure Application Insights を使ってカスタム KPI ダッシュボードを作成する](app/tutorial-app-dashboards.md)」を参照してください。 



## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) は、長期的な KPI 傾向を分析するレポートと共に、ビジネス中心のダッシュボードとレポートを作成するのに便利です。 Power BI のデータセットに[ログ クエリの結果をインポート](./logs/log-powerbi.md)して、その機能を利用できます。たとえば、さまざまなソースのデータを組み合わせたり、Web およびモバイル デバイス上でレポートを共有したりすることができます。

![IT の運用に関する Power BI レポートの例を示すスクリーンショット。](media/visualizations/power-bi.png)

Power BI の一般的なシナリオには次のようなものがあります。

- 高度な視覚化。
- ズームインやクロスフィルタリングなどの広範なインタラクティビティ。
- 組織全体で共有が簡単。
- 複数のデータ ソースからの他のデータとの統合。
- キューブにキャッシュされる結果のパフォーマンスが向上。



## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) は、運用ダッシュボードに優れたオープン プラットフォームです。 これは、運用上のインシデントを検出、分離、トリアージする場合や、Azure と Azure 以外のデータ ソース (オンプレミス、サードパーティ製のツール、他のクラウド内のデータ ストアなど) の視覚化を組み合わせる場合に役立ちます。 Grafana には Dynatrace、New Relic、App Dynamics などの APM ツール用の一般的なプラグインとダッシュボード テンプレートがあり、ユーザーはこれらを使用して、他のツールによって収集されたスタック内の上位にある他のメトリックと共に Azure プラットフォーム データを視覚化できます。 また、1 つのウィンドウでマルチクラウド監視を行うための AWS CloudWatch および GCP BigQuery プラグインもあります。




お使いの Azure サブスクリプションに [Grafana 用の Azure Monitor データ ソース プラグイン](visualize/grafana-plugin.md)を追加することで、Azure のメトリック データを視覚化することができます。

![Grafana の視覚化を示すスクリーンショット。](media/visualizations/grafana.png)


Grafana の一般的なシナリオには次のようなものがあります。

- 時系列とイベントのデータを 1 つの視覚化パネルに結合する。
- ユーザーが選択した動的変数に基づいて動的ダッシュボードを作成する。
- コミュニティで作成され、サポートされているテンプレートからダッシュボードを作成する。
- 任意のクラウド プロバイダーまたはオンプレミスで実行される、ベンダーに依存しない BCDR シナリオを作成する。

## <a name="azure-monitor-partners"></a>Azure Monitor パートナー
一部の Azure Monitor パートナーにより、視覚化機能が提供されます。 Microsoft が評価したパートナーの一覧については、「[Azure Monitor パートナーとの統合](./partners.md)」を参照してください。 Azure Monitor パートナーにより、時間を節約するためのすぐに利用できる視覚化が提供される場合があります。ただし、これらのソリューションには追加コストが発生する可能性があります。


## <a name="custom-application"></a>カスタム アプリケーション
さらに、Azure Monitor で REST API を通じてアクセスするメトリックとログのデータを使用して、独自のカスタム Web サイトとアプリケーションを作成できます。 これにより、UI、視覚化、インタラクティビティ、機能における完全な柔軟性が得られます。


## <a name="next-steps"></a>次のステップ
- 「[アラートと自動アクション](best-practices-alerts.md)」を参照して、Azure Monitor データからアラートと自動アクションを定義します。