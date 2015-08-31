<properties
	pageTitle="Application Insights ポータルの使用"
	description="Application Insights による利用状況分析の概要"
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="multiple"
	ms.topic="article" 
	ms.date="08/17/2015"
	ms.author="awills"/>

# Application Insights ポータルの使用

[Application Insights をプロジェクトに設定](app-insights-get-started.md)すると、アプリのパフォーマンスと使用状況に関するテレメトリ データが、[Azure ポータル](https://portal.azure.com)でプロジェクトの Application Insights リソースに表示されます。

## Azure でテレメトリを検索する

[Azure ポータル](https://portal.azure.com)にサインインし、アプリ用に作成した Application Insights リソースを参照します。

![[参照] をクリックし、[Application Insights]、アプリの順に選択します。](./media/app-insights-portal/00-start.png)

[概要] ページには、いくつかの基本的なテレメトリとその他のリンクが示されます。内容は、アプリの種類によって異なり、カスタマイズすることができます。

## アプリの [概要] ブレード

アプリの [概要] ブレード (ページ) には、パフォーマンスと使用状況を監視するための最も重要なグラフが表示されます。内容は、アプリの種類によって異なり、いずれの場合にもカスタマイズすることができます。


### [概要] ブレードのカスタマイズ 

概要を表示する対象を選択します。[カスタマイズ] では、セクション タイトルの挿入、タイルやグラフのドラッグ、項目の削除、ギャラリーからの新しいタイルやグラフの追加ができます。

![[...] をクリックして、カスタマイズします。タイルやグラフをドラッグします。ギャラリーからタイルを追加します。](./media/app-insights-portal/020-customize.png)


## 独自のメトリック グラフとグリッドを作成する

### グラフおよびグリッドの編集

新しいグラフをブレードに追加するには:

![メトリックス エクスプローラーで、[グラフの追加] を選択します](./media/app-insights-metrics-explorer/04-add.png)

表示内容を編集する既存のグラフまたは新しいグラフを選択します。

![1 つまたは複数のメトリックを選択します](./media/app-insights-metrics-explorer/08-select.png)

1 つのグラフに複数のメトリックを表示できますが、一緒に表示できるメトリックの組み合わせに関して制限があります。1 つのメトリックを選択するとすぐに、他の特定のメトリックが無効になります。

アプリに[カスタム メトリック](app-insights-api-custom-events-metrics.md#track-metric)を組み込んでいる場合 (TrackMetric と TrackEvent の呼び出し) は、これらのメトリックがここに表示されます。

### データの分割

グラフまたはグリッドを選択します。[グループ化] をオンにし、グループ化のために使用するプロパティを選択します。

![[グループ化] をオンにし、[グループ化] でプロパティを選択します](./media/app-insights-metrics-explorer/15-segment.png)

アプリに組み込んだカスタム メトリックが[プロパティ値](app-insights-api-custom-events-metrics.md#properties)を持つ場合は、この一覧でそのプロパティを選択できます。

分割されたデータを表示するのにグラフが小さすぎる場合は、 高さを調整することができます。

![スライダーを調整します](./media/app-insights-metrics-explorer/18-height.png)

### データのフィルター選択

選択したプロパティ値のセットのメトリックのみを表示するには:

![[フィルター] をクリックした後、プロパティを展開し、目的の値にチェック マークを付けます](./media/app-insights-metrics-explorer/19-filter.png)

特定のプロパティの値を選択しない場合、すべての値を選択するのと同じ意味になり、そのプロパティに基づくフィルターはオフになります。

各プロパティ値の横に表示されるイベント数に注目してください。あるプロパティの値を選択すると、その他のプロパティの値の横に表示される数値が調整されます。

### メトリック ブレードの保存

作成したグラフはお気に入りとして保存できます。組織のアカウントを使用している場合は、これを他のチーム メンバーと共有するかどうかを選択できます。

![[お気に入り] を選択します](./media/app-insights-metrics-explorer/21-favorite-save.png)

このブレードをもう一度表示するには、**概要ブレードに移動**し、[お気に入り] を開きます。

![概要ブレードで [お気に入り] を選択します](./media/app-insights-metrics-explorer/22-favorite-get.png)

相対的な時間範囲を選択して保存した場合、ブレードは最新のメトリックで更新されます。絶対的な時間範囲を選択した場合は、毎回同じデータが表示されます。

### ブレードのリセット

ブレードを編集した後で元の保存済みセットに戻すには、[リセット] をクリックします。

![メトリックス エクスプローラーの上部にあるボタン](./media/app-insights-metrics-explorer/17-reset.png)

## 検索ページの作成

診断検索を開きます。

![Open diagnostic search](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

[フィルター] ブレードを開き、表示するイベントの種類を選択します (後でブレードを開いたときに表示されるフィルターを復元するには、[リセット] をクリックします)。

![[フィルター] を選択し、テレメトリの種類を選択します](./media/app-insights-diagnostic-search/02-filter-req.png)

### プロパティの値に基づくフィルター選択

プロパティの値に基づいてイベントをフィルター選択できます。使用できるプロパティは、選択したイベントの種類に依存します。

たとえば、特定の応答コードを持つ要求を選択できます。

![プロパティを展開し、値を選択します](./media/app-insights-diagnostic-search/03-response500.png)

特定のプロパティの値を選択しない場合、すべての値を選択するのと同じ意味になり、そのプロパティに基づくフィルターはオフになります。


### 検索の絞り込み

フィルター値の右側の値は、現在のフィルター選択されたセットに含まれるイベントの発生回数を表します。

この例では、500 エラーの大部分が `Reports/Employees` 要求であることがわかります。

![プロパティを展開し、値を選択します](./media/app-insights-diagnostic-search/04-failingReq.png)

さらに、この期間中に発生している他のイベントを確認するには、**[未定義のプロパティを持つイベントを含める]** チェック ボックスをオフにします。

### 検索条件の保存

必要なフィルターをすべて設定した後、この検索条件をお気に入りとして保存できます。組織のアカウントで作業している場合は、これを他のチーム メンバーと共有するかどうかを選択できます。

![[お気に入り] をクリックし、名前を設定して [保存] をクリックします](./media/app-insights-diagnostic-search/08-favorite-save.png)


検索条件をもう一度表示するには、**概要ブレードに移動**し、[お気に入り] を開きます。

![[お気に入り] タイル](./media/app-insights-diagnostic-search/09-favorite-get.png)

相対的な時間範囲を選択して保存した場合は、ブレードを開くたびに最新のデータが表示されます。絶対的な時間範囲を選択して保存した場合は、毎回同じデータが表示されます。

<!---HONumber=August15_HO8-->