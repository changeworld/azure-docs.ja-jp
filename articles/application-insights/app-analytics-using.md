<properties 
	pageTitle="Analytics の使用 - Application Insights の強力な検索ツール" 
	description="Application Insights の強力な診断検索ツールである Analytics の使用。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2016" 
	ms.author="awills"/>




# Application Insights の Analytics の使用

[Analytics](app-analytics.md) を使用すると、[Application Insights](app-insights-overview.md) によってアプリから収集されたテレメトリに対して強力なクエリを実行できます。ここでは、Analytics のクエリ言語について説明します。

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Analytics を開く

Application Insights で、アプリのホーム リソースから [Analytics] をクリックします。

![portal.azure.com で Application Insights リソースを開き、[Analytics] をクリックします。](./media/app-analytics/001.png)

インラインのチュートリアルでは、実行可能な操作が説明されています。

[ここにはさらに広範なツアー](app-analytics-tour.md)があります。

## クエリの作成

左側に表示されるテーブルの名前で始まるクエリを作成します。`|` を使用して、[演算子](app-analytics-queries.md)のパイプラインを作成します。


![](./media/app-analytics-using/150.png)

* クエリに空白行を入れないでください。
* クエリには改行文字 1 つを使用できます。
* クエリを空白行で区切って、ウィンドウ内に複数のクエリを収めることができます。
* クエリを実行するには、**クエリの内部または末尾にカーソルを置いて**、[検索] をクリックします。


![](./media/app-analytics-using/130.png)

* クエリ ウィンドウの内容の保存および取り消しを行うことができます。

![](./media/app-analytics-using/140.png)

## 結果の配置

表示する列を選択することができます。任意のアイテムを展開すると、返された列のすべての値が表示されます。

![](./media/app-analytics-using/030.png)

> [AZURE.NOTE] 列の先頭をクリックすると、Web ブラウザーに表示される結果の順序を簡単に変更できます。ただし、大きな結果セットの場合は、ブラウザーにダウンロードされる行数が制限されます。したがって、この並べ替え方法では、実際の最上位アイテムまたは最下位アイテムが表示されない場合があります。その場合は、[top](app-analytics-queries.md#top-operator) または [sort](app-analytics-queries.md#sort-operator) 演算子を使用する必要があります。

サーバーから膨大なテーブルがダウンロードされないように、[take](app-analytics-queries.md#take-operator)、[top](app-analytics-queries.md#top-operator)、または [summarize](app-analytics-queries.md#summarize-operator) 演算子を使用することをお勧めします。クエリ 1 件あたり約 10,000 行の自動制限が必ず適用されます。


## ダイアグラム

必要なダイアグラムの種類を選択します。

![](./media/app-analytics-using/230.png)

種類が適切な複数の列がある場合は、x 軸と y 軸を選択して、結果を分割するディメンションの列を選択できます。

![](./media/app-analytics-using/100.png)

既定では、結果は最初にテーブルとして表示されます。ダイアグラムを手動で選択します。ただし、クエリの最後に[render ディレクティブ](app-analytics-queries.md#render-directive)を使用して、ダイアグラムを選択できます。

## Excel へのエクスポート

クエリを実行した後は、.csv ファイルをダウンロードできます。**[Excel にエクスポート]** をクリックします。

## Power BI へのエクスポート

1. クエリにカーソルを置いて、**[Power BI にエクスポート]** を選択します。

    ![](./media/app-analytics-using/240.png)

    これにより、M スクリプト ファイルがダウンロードされます。

3. M 言語スクリプトを Power BI Desktop 詳細クエリ エディターにコピーします。
 * エクスポートされたファイルを開きます。
 * Power BI Desktop で **[データの取得]、[空のクエリ]、[詳細エディター]** を選択して、M 言語のスクリプトを貼り付けます。

    ![](./media/app-analytics-using/250.png)

4. 必要に応じて資格情報を編集します。これでレポートを作成できるようになります。

    ![](./media/app-analytics-using/260.png)



[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016------>