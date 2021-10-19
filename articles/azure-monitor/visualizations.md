---
title: Azure Monitor からのデータを視覚化する | Microsoft Docs
description: Azure Monitor に格納されたメトリックとログ データを視覚化するために使用可能な方法の概要を把握します。
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 07/28/2021
ms.openlocfilehash: 257ca33952283ba3989ce11a2d933af3feceb8ac
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612353"
---
# <a name="visualize-data-from-azure-monitor"></a>Azure Monitor からのデータを視覚化する
この記事では、Azure Monitor に格納されたメトリックとログ データを視覚化するために使用可能な方法について概説します。

チャートやグラフなどで視覚化すると、監視データを分析して問題をドリルダウンし、パターンを特定しやすくなります。 使用するツールによっては、組織の内外の他のユーザーと視覚化を共有するオプションも用意されています。

## <a name="workbooks"></a>Workbooks
[Workbooks](./visualize/workbooks-overview.md) は、データの詳細な分析情報、調査、およびチーム内のコラボレーションを提供する対話型のドキュメントです。 ブックは、トラブルシューティングガイドやインシデントの事後分析に特に有用です。

![ページビューの分析、使用状況、ページで費やされた時間を含む、ブックからの 3 ページのスクリーンショットを示す図。](media/visualizations/workbook.png)

ワークブックの利点は次のとおりです。

- メトリックとログの両方をサポート。
- テーブル内の要素を選択すると、関連付けられたグラフと視覚化が動的に更新される、対話型レポートを有効にするパラメーターをサポート。
- ドキュメントのようなフロー。
- 個人または共有ブックのオプション。
- 簡単で協調的な作成エクスペリエンス。
- GitHub ベースのパブリック テンプレート ギャラリーをサポートするテンプレート。


## <a name="azure-dashboards"></a>Azure ダッシュボード
[Azure ダッシュボード](../azure-portal/azure-portal-dashboards.md)は、Azure の主要なダッシュボード テクノロジです。 これは、Azure インフラストラクチャとサービスで 1 つのウィンドウを提供するのに役立つため、重要な問題をすばやく特定できます。

![カスタマイズ可能な情報が表示されている Azure ダッシュボードの例を示すスクリーンショット。](media/visualizations/dashboard.png)

次のビデオ チュートリアルでは、ダッシュボードの作成について説明しています。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

ダッシュボードの利点は次のとおりです。

- Azure との緊密な統合。 視覚化は、[メトリックス エクスプローラー](essentials/metrics-charts.md)、[Log Analytics](logs/log-analytics-overview.md)、[Application Insights](app/app-insights-overview.md) など、複数の Azure ページからダッシュボードにピン留めできます。
- メトリックとログの両方をサポート。
- [メトリックス エクスプローラー](essentials/metrics-charts.md)、[ログ クエリ](logs/log-query-overview.md)、[マップ](app/app-map.md)からの出力など、複数のソースからのデータを結合する機能と、[Application Insights](app/app-insights-overview.md) での可用性。
- [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) との統合による個人または共有ダッシュボードのオプション。
- 自動更新。 メトリックの更新は時間の範囲によって異なり、最短 5 分です。 ログは、1 時間ごとに更新されます。 必要に応じて手動で更新するには、視覚化で **[更新]** アイコンを選択するか、ダッシュボード全体を更新します。
- タイムスタンプとカスタム パラメーターでパラメーター化されたメトリック ダッシュボード。
- 柔軟なレイアウト オプション。
- 全画面表示モード。


制限事項は次のとおりです。

- ログの視覚化に対する制御が限定的であり、データ テーブルはサポートされない。 データ系列の合計数は 50 に制限され、それ以上のデータ系列は "_その他_" のバケットの下にグループ化される。
- ログのグラフに対してカスタム パラメーターはサポートされない。
- ログのグラフの期間は制限される (過去 30 日間)。
- ログのグラフは、共有ダッシュボードにピン留めする必要がある。
- ダッシュボード データとのインタラクティビティはありません。
- 制限されたコンテキストのドリルダウン。

## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) は、運用ダッシュボードと、パフォーマンスおよび可用性データの視覚化に優れたオープン プラットフォームです。 運用上のインシデントの検出、分離、トリアージに役立ちます。 Grafana には、Azure Monitor のサポートが組み込まれており、難しい設定が不要な [Azure Monitor プラグイン](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/)が含まれています。 次の 3 つの Azure サービスで収集されたデータの視覚化がサポートされています。
- Azure Monitor メトリック: Azure リソースで収集されたデータからの数値の時系列データ。 
- Azure Monitor ログ: 強力な Kusto クエリ言語 (KQL) を使用してクエリを実行できるようにする Azure リソースからのログおよびパフォーマンス データ。
- Azure Resource Graph: サブスクリプション全体で Azure リソースのクエリをすばやく実行して識別します。

Grafana はさまざまなデータ ソースと連携し、Azure、オンプレミス、またはその他のクラウドのリソースからの監視データを組み合わせたダッシュボードとパネルを作成できるようにします。 

![Grafana の視覚化を示すスクリーンショット。](media/visualizations/grafana.png)

> [!IMPORTANT]
> Internet Explorer 11 は、v6.0 より前のバージョンの Grafana でのみ完全にサポートされています。[Grafana でサポートされるブラウザー](https://grafana.com/docs/grafana/latest/installation/requirements/#supported-web-browsers)に関するページを参照してください。

Grafana の利点は次のとおりです。

- 高度な視覚化。
- Azure 以外のデータ ソースを含む、充実したデータ ソース エコシステム。
- ズームインなどのデータ インタラクティビティ。
- イベント データをグラフに統合できるようにする注釈。
- [Azure Monitor 変数](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/template-variables/)など、パラメーターのサポート。

制限事項は次のとおりです。

- Azure Resource Manager を使用してダッシュボードおよびモデルを管理することはできない。
- 追加の Grafana インフラストラクチャをサポートするためのコストまたは Grafana クラウドの追加コスト。
## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) は、長期的な KPI 傾向を分析するレポートと共に、ビジネス中心のダッシュボードとレポートを作成するのに便利です。 Power BI のデータセットに[ログ クエリの結果をインポート](visualize/powerbi.md)して、その機能を利用できます。たとえば、さまざまなソースのデータを組み合わせたり、Web およびモバイル デバイス上でレポートを共有したりすることができます。

![IT の運用に関する Power BI レポートの例を示すスクリーンショット。](media/visualizations/power-bi.png)

Power BI の利点は次のとおりです。

- 高度な視覚化。
- ズームインやクロスフィルタリングなどの広範なインタラクティビティ。
- 組織全体で共有が簡単。
- 複数のデータ ソースからの他のデータとの統合。
- キューブにキャッシュされる結果のパフォーマンスが向上。

制限事項は次のとおりです。

- ログはサポートされるがメトリックはサポートされない。
- Azure の統合はなし。 Azure Resource Manager を使用してダッシュボードおよびモデルを管理することはできない。
- 構成するには、クエリ結果を Power BI モデルにインポートする必要がある。 
- 結果のサイズと更新に制限がある。
- データ更新に制限がある (1 日あたり 8 回)。


## <a name="azure-monitor-partners"></a>Azure Monitor パートナー
一部の Azure Monitor パートナーにより、視覚化機能が提供されます。 Microsoft が評価したパートナーの一覧については、「[Azure Monitor パートナーとの統合](./partners.md)」を参照してください。 

Azure Monitor パートナーにより、難しい設定が不要な視覚化が提供され、時間を節約できる場合があります。 

制限事項は次のとおりです。

- 追加のコストが発生する可能性がある。
- パートナー オファリングを調査して評価するための時間が必要。

## <a name="your-own-custom-application"></a>独自のカスタム アプリケーション
Azure Monitor のメトリックおよびログ データには、任意の REST クライアントを使用することにより、API を介してアクセスできます。 その後、独自のカスタム Web サイトやアプリケーションを構築できます。

カスタム アプリケーションを構築する利点は次のとおりです。

- UI、視覚化、インタラクティビティ、および機能における完全な柔軟性。
- メトリックおよびログ データを他のデータ ソースと結合できる。

大きな決定の 1 つとして、エンジニアリング作業が必要。

## <a name="azure-monitor-views"></a>Azure Monitor ビュー

> [!IMPORTANT]
> ビューは今後非推奨となる予定です。 ビューをブックに変換する方法のガイダンスについては、[移行ガイド](visualize/view-designer-conversion-overview.md)を参照してください。

[Azure Monitor のビュー](visualize/view-designer.md) では、ログ データを使用して、カスタム視覚化を作成できます。 [監視ソリューション](insights/solutions.md)では、ビューを使用して、収集したデータを表示します。


![[コンテナー監視ソリューション] タイルと、それが選択されたときに開く詳細な Azure Monitor ビューを示すスクリーンショット。](media/visualizations/view.png)

ビューの利点は次のとおりです。

- ログ データの高度な視覚化。
- ビューを他のリソース グループとサブスクリプションに転送するためのビューをエクスポートおよびインポートすることができる。
- ワークスペースおよび監視ソリューションと共に、Azure Monitor 管理モデルに統合。
- カスタム パラメーターの[フィルター処理](visualize/view-designer-filters.md)。
- 複数レベルのドリルイン (別のビューにドリルインするビュー) のサポートによるインタラクティビティ。

制限事項は次のとおりです。

- ログはサポートされるがメトリックはサポートされない。
- 個人用ビューはなし。 ビューは、ワークスペースへのアクセス権を持つすべてのユーザーが使用可能。
- 自動更新なし。
- 制限されたレイアウト オプション。
- 複数のワークスペースや Application Insights アプリケーションに対する横断的なクエリ実行がサポートされない。
- クエリの応答サイズは 8 MB に制限され、クエリの実行時間は 110 秒に制限される。

## <a name="next-steps"></a>次の手順
- [Azure Monitor で収集されたデータ](data-platform.md)について学習します。
- [Azure ダッシュボード](../azure-portal/azure-portal-dashboards.md)について学習します。
- [メトリックス エクスプローラー](essentials/metrics-getting-started.md)について学習します。
- [ブック](./visualize/workbooks-overview.md)について学習します。
- [Power BI へのログ データのインポート](./visualize/powerbi.md)について学習します。
- [Grafana 用 Azure Monitor データ ソース プラグイン](./visualize/grafana-plugin.md)について学習します。
- [Azure Monitor のビュー](visualize/view-designer.md)について学習します。
