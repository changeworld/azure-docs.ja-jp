---
title: Analytics の使用 - Application Insights の強力な検索ツール | Microsoft Docs
description: 'Application Insights の強力な診断検索ツールである Analytics を使用します。 '
services: application-insights
documentationcenter: ''
author: danhadari
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: danha

---
# <a name="using-analytics-in-application-insights"></a>Application Insights の Analytics の使用
[Analytics](app-insights-analytics.md) は、[Application Insights](app-insights-overview.md) の強力な検索機能です。 ここでは、Analytics のクエリ言語について説明します。

* **[紹介ビデオを見る](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**。
* **[シミュレーション データで Analytics を試す](https://analytics.applicationinsights.io/demo)** (ご使用のアプリからまだ Application Insights にデータが送信されていない場合)。

## <a name="open-analytics"></a>Analytics を開く
Application Insights のアプリのホーム リソースで、[Analytics] をクリックします。

![portal.azure.com で Application Insights リソースを開き、[Analytics] をクリックします。](./media/app-insights-analytics-using/001.png)

インラインのチュートリアルでは、実行可能な操作が説明されています。

[ここにはさらに広範なツアー](app-insights-analytics-tour.md)があります。

## <a name="query-your-telemetry"></a>テレメトリのクエリを実行する
### <a name="write-a-query"></a>クエリを記述します
![スキーマの表示](./media/app-insights-analytics-using/150.png)

左側の一覧に表示されるテーブルのいずれかの名前 (または [range](app-insights-analytics-reference.md#range-operator) や [union](app-insights-analytics-reference.md#union-operator) 演算子) から始めます。 `|` を使用して、 [演算子](app-insights-analytics-reference.md#queries-and-operators)のパイプラインを作成します。 IntelliSense によって、使用できる演算子と、式のいくつかの要素が示されます。

[Analytics 言語の概要](app-insights-analytics-tour.md)と[言語のリファレンス](app-insights-analytics-reference.md)に関するページを参照してください。

### <a name="run-a-query"></a>クエリを実行する
![Running a query](./media/app-insights-analytics-using/130.png)

1. クエリでは単一改行を使用できます。
2. 実行するクエリの内部または末尾にカーソルを置きます。
3. [実行] をクリックしてクエリを実行します。
4. クエリに空白行を入れないでください。 いくつかの個別のクエリを、空白行で区切ることによって、1 つのクエリ タブに保持することができます。 カーソルがある 1 つだけが実行されます。

### <a name="save-a-query"></a>クエリを保存する
![Saving a query](./media/app-insights-analytics-using/140.png)

1. 現在のクエリ ファイルを保存します。
2. 保存したクエリ ファイルを開きます。
3. 新しいクエリ ファイルを作成します。

## <a name="see-the-details"></a>詳細を表示する
結果の任意の行を展開すると、プロパティの完全な一覧が表示されます。 値が構造化されているプロパティ (たとえば、カスタム ディメンション、例外に一覧表示されるスタックなど) は、さらに展開することができます。

![Expand a row](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>結果を配置する
クエリから返された結果は、並べ替え、フィルター処理、改ページ、およびグループ化することができます。

> [!NOTE]
> ブラウザーでの並べ替え、グループ化、およびフィルター処理では、クエリは再実行されません。 最後のクエリによって返された結果を再配置するだけです。 
> 
> 結果が返される前に、サーバーでこれらのタスクを実行するには、[sort](app-insights-analytics-reference.md#sort-operator)、[summarize](app-insights-analytics-reference.md#summarize-operator)、および [where](app-insights-analytics-reference.md#where-operator) 演算子を使用してクエリを記述します。
> 
> 

表示する列を選択し、列ヘッダーをドラッグして再配置したり、境界をドラッグして列の幅を変更したりします。

![Arrange columns](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>項目の並べ替えとフィルター処理を行う
結果を並べ替えるには、列のヘッダーをクリックします。 もう一度クリックすると、もう 1 つの方法で並べ替えられ、3 回目のクリックで、クエリから返された元の順序に戻ります。

検索を絞り込むには、フィルター アイコンを使用します。

![Sort and filter columns](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>項目をグループ化する
複数の列で並べ替えるには、グループ化を使用します。 まず有効にしてから、列ヘッダーをテーブルの上の領域にドラッグします。

![グループ](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results?"></a>結果の一部が表示されない場合
ポータルから返される結果には、約 10k 行という制限があります。 制限を超えると、警告が表示されます。 そうなった場合、テーブルで結果を並べ替えると、実際の結果のうちの先頭または末尾の部分が表示されないことがあります。 

制限に達しないようにすることをお勧めします。 次のような演算子を使用してください。

* [where timestamp > ago(3d)](app-insights-analytics-reference.md#where-operator)
* [top 100 by timestamp](app-insights-analytics-reference.md#top-operator) 
* [take 100](app-insights-analytics-reference.md#take-operator)
* [summarize ](app-insights-analytics-reference.md#summarize-operator) 

## <a name="diagrams"></a>ダイアグラム
必要なダイアグラムの種類を選択します。

![ダイアグラムの種類の選択](./media/app-insights-analytics-using/230.png)

種類が適切な複数の列がある場合は、x 軸と y 軸を選択して、結果を分割するディメンションの列を選択できます。

既定では、結果は最初にテーブルとして表示されるので、ダイアグラムを手動で選択します。 ただし、クエリの末尾に [render ディレクティブ](app-insights-analytics-reference.md#render-directive) を使用して、ダイアグラムを選択できます。

## <a name="pin-to-dashboard"></a>Pin to dashboard
ピン アイコンをクリックするだけで、いずれかの[共有ダッシュボード](app-insights-dashboards.md)にダイアグラムまたはテーブルをピン留めすることができます。 (この機能を有効にするには、[アプリの料金パッケージのアップグレード](app-insights-pricing.md)が必要になる場合があります)。 

![ピンのクリック](./media/app-insights-analytics-using/pin-01.png)

つまり、Web サービスのパフォーマンスや使用状況の監視に利用するダッシュボードを構成するときに、複雑な分析を他のメトリックに一緒に組み込むことができます。 

列の数が 4 列以下のテーブルはダッシュボードにテーブルをピン留めすることができます。 最初の 7 行のみが表示されます。

#### <a name="dashboard-refresh"></a>ダッシュボードの更新
ダッシュボードにピン留めされているグラフは、約 30 分おきにクエリが再実行されるときに自動的に更新されます。

#### <a name="automatic-simplifications"></a>自動簡略化
グラフをダッシュボードにピン留めすると、特定の簡略化が適用されることがあります。

多数の不連続ビン (通常は横棒グラフ) が表示されているグラフをピン留めすると、割合の低いビンは "その他" のビンに自動的にグループ化されます。 次のクエリを例にします。

    requests | summarize count_search = count() by client_CountryOrRegion

上のクエリは、Analytics では次のようになります。

![底部が長いグラフ](./media/app-insights-analytics-using/pin-07.png)

ところが、ダッシュボードにピン留めすると次のようになります。

![ビンが制限されたグラフ](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Excel へのエクスポート
クエリを実行したら、.csv ファイルをダウンロードできます。 **[エクスポート]**をクリックしてから Excel を選択します。

## <a name="export-to-power-bi"></a>Power BI へのエクスポート
1. クエリにカーソルを置いて、 **[エクスポート]**を選択してから Power BI を選択します。
   
    ![](./media/app-insights-analytics-using/240.png)
   
    これにより、M スクリプト ファイルがダウンロードされます。
2. M 言語スクリプトを Power BI Desktop 詳細クエリ エディターにコピーします。
   
   * エクスポートされたファイルを開きます。
   * Power BI Desktop で、 **[データを取得]、[空のクエリ]、[詳細エディター]** を選択し、M 言語のスクリプトを貼り付けます。
     
     ![](./media/app-insights-analytics-using/250.png)
3. 必要に応じて資格情報を編集します。これでレポートを作成できるようになります。
   
    ![](./media/app-insights-analytics-using/260.png)

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!--HONumber=Oct16_HO2-->


