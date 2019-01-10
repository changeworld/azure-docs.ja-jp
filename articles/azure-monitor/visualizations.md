---
title: Azure Monitor からのデータを視覚化する | Microsoft Docs
description: Azure Monitor に格納されたメトリックとログ データを視覚化するために使用可能な方法の概要を提供します。
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/24/2018
ms.author: bwren
ms.openlocfilehash: 9b160c011a8a9507698443446ad5fe908b32fb9e
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54049964"
---
# <a name="visualizing-data-from-azure-monitor"></a>Azure Monitor からのデータを視覚化する
この記事では、Azure Monitor に格納されたログとメトリック データを視覚化するために使用可能な方法の概要を提供します。

チャートやグラフなどの視覚化は、監視データを分析して問題をドリルダウンし、パターンを特定するのに役立ちます。 使用するツールによっては、組織の内外の他のユーザーと視覚化を共有するオプションもあります。

## <a name="azure-dashboards"></a>Azure ダッシュボード
[Azure ダッシュボード](../azure-portal/azure-portal-dashboards.md)は、Azure の主要なダッシュボード テクノロジです。 ダッシュボードは、重要な問題をすばやく特定できるように、Azure インフラストラクチャとサービスに関する 1 つのウィンドウを提供するという点で特に役立ちます。

![ダッシュボード](media/visualizations/dashboard.png)

### <a name="advantages"></a>長所
- Azure との緊密な統合。 視覚化は、メトリック分析、ログ分析、Application Insights など、複数の Azure ページからダッシュボードにピン留めできます。
- メトリックとログの両方をサポート。
- [メトリックス エクスプローラー](../azure-monitor/platform/metrics-charts.md)からの出力、[Log Analytics クエリ](../azure-monitor/log-query/log-query-overview.md)、および Application Insights の[マップ](../azure-monitor/app/app-map.md)と[可用性]()など、複数のソースからデータを結合します。
- 個人または共有のダッシュボードのオプション。 Azure [ロール ベースの認証 (RBAC)](../role-based-access-control/overview.md) との統合。
- 自動更新。 メトリックの更新は、最短 5 分の時間範囲に依存します。 ログは 1 分で更新されます。
- タイムスタンプとカスタム パラメーターでパラメーター化されたメトリック ダッシュボード。
- 柔軟なレイアウト オプション。
- 全画面表示モード。


### <a name="limitations"></a>制限事項
- ログの視覚化に対する制御が限定的であり、データ テーブルはサポートされません。 データ系列の合計数は 10 に制限されていて、それ以上のデータ系列は _その他_ のバケットの下にグループ化されます。
- ログのグラフに対するカスタム パラメーターのサポートはありません。
- ログのグラフは、過去 30 日間に制限されます。
- ログのグラフは、共有ダッシュボードにのみピン留めできます。
- ダッシュボード データとのインタラクティビティはありません。
- 制限されたコンテキストのドリルダウン。

## <a name="azure-monitor-views"></a>Azure Monitor ビュー
[Azure Monitor のビュー](../log-analytics/log-analytics-view-designer.md)では、ログ データを使用して、カスタム視覚化を作成できます。 これらのビューは、[監視ソリューション](insights/solutions.md)が収集したデータを表示するために使用します。

![表示](media/visualizations/view.png)

### <a name="advantages"></a>長所
- ログ データの高度な視覚化。
- ビューを他のリソース グループとサブスクリプションに転送するためのビューのエクスポートとインポート。
- ワークスペースと監視ソリューションを持つ Log Analytics 管理モデルへの統合。
- カスタム パラメーターの[フィルター処理](platform/view-designer-filters.md)。
- 対話型、複数レベルのドリルイン (別のビューに絞り込むビュー) をサポート

### <a name="limitations"></a>制限事項
- ログはサポートされるがメトリックはサポートされない。
- 個人用ビューはなし。 ワークスペースへのアクセス権を持つすべてのユーザーが使用可能。
- 自動更新なし。
- 制限されたレイアウト オプション。
- 複数のワークスペースや Application Insights アプリケーションに対する横断的なクエリ実行がサポートされない。
- クエリの応答サイズは 8 MB に制限され、クエリの実行時間は 110 秒に制限される。



## <a name="application-insights-workbooks"></a>Application Insights Workbooks
[Workbooks](../azure-monitor/app/usage-workbooks.md) は、データの詳細な分析情報、調査、およびチーム内のコラボレーションを提供する対話型のドキュメントです。 Workbooks が役立つ具体的な例は、トラブルシューティング ガイドとインシデントの事後分析です。

![Workbooks](media/visualizations/workbook.png)

### <a name="advantages"></a>長所
- メトリックとログの両方をサポート。
- テーブル内の要素を選択すると、関連付けられたグラフと視覚化が動的に更新される、対話型レポートを有効にするパラメーターをサポート。
- ドキュメントのようなフロー。
- 個人または共有の Workbooks のオプション。
- 簡単でコラボレーションしやすい作成エクスペリエンス。
- テンプレートでの GitHub ベースのパブリック テンプレート ギャラリーのサポート。

### <a name="limitations"></a>制限事項
- 自動更新なし。
- ダッシュボードのような密度の高いレイアウトがなく、Workbooks が 1 つのウィンドウとして表示されるため、利便性には劣りますが、 詳細な分析情報を提供することを目的としています。


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) は、ビジネス中心のダッシュボードとレポート、および長期的な KPI 傾向の分析レポートを作成するのに特に便利です。 [ログ クエリの結果を Power BI のデータセットにインポート](../log-analytics/log-analytics-powerbi.md)できます。そうすることでさまざまなソースのデータを組み合わせたり、Web とモバイル デバイス上でレポートを共有したりするような機能を利用できます。

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>長所
- 高度な視覚化。
- ズームインやクロスフィルタリングを含む広範なインタラクティビティ。
- 組織全体で共有が簡単。
- 複数のデータ ソースからの他のデータとの統合。
- キューブにキャッシュされる結果のパフォーマンスが向上。


### <a name="limitations"></a>制限事項
- ログはサポートされるがメトリックはサポートされない。
- Azure の統合はなし。 Azure Resource Manager からダッシュボードとモデルを管理できない。
- 構成するには、クエリ結果を Power BI モデルにインポートする必要がある。 結果のサイズと更新への制限。
- 1 日 8 回に制限されたデータ更新。


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) は、運用ダッシュボードに優れたオープン プラットフォームです。 運用上のインシデントの検出、分離、トリアージに特に役立ちます。 お使いの Azure サブスクリプションに [Grafana Azure Monitor データ ソース プラグイン](../azure-monitor/platform/grafana-plugin.md)を追加することで、Azure のメトリック データを視覚化することができます。

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>長所
- 高度な視覚化。
- データ ソースの充実したエコシステム。
- ズーム インなどのデータ インタラクティビティ。
- パラメーターのサポート。

### <a name="limitations"></a>制限事項
- メトリックはサポートされるがログはサポートされない。
- Azure の統合はなし。 Azure Resource Manager からダッシュボードとモデルを管理できない。
- 追加の Grafana インフラストラクチャをサポートするためのコストまたは Grafana クラウドの追加コスト。


## <a name="build-your-own-custom-application"></a>独自のカスタム アプリケーションのビルド
Azure Monitor のメトリック データとログ データには、その API を通じ、任意の REST クライアントを使用してアクセスできます。これにより、独自のカスタム Web サイトとアプリケーションを構築できます。

### <a name="advantages"></a>長所
- UI、視覚化、インタラクティビティ、および機能における完全な柔軟性。
- メトリックとログ データを他のデータ ソースと結合する。

### <a name="disadvantages"></a>短所
- 多くのエンジニア リング作業が必要。


## <a name="next-steps"></a>次の手順
- [Azure Monitor によって収集されたデータ](platform/data-collection.md)について学習します。
- [Azure ダッシュボード](../azure-portal/azure-portal-dashboards.md)について学習します。
- [Azure Monitor のビュー](platform/view-designer.md)について学習します。
- [Application Insights の Workbooks](../azure-monitor/app/usage-workbooks.md) について学習します。
- [ログ データの Power BI へのインポート](../azure-monitor/platform/powerbi.md)について学習します。
- [Grafana Azure Monitor データ ソース プラグイン](../azure-monitor/platform/grafana-plugin.md)について学習します。

