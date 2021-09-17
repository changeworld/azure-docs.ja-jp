---
title: Log Analytics のチュートリアル
description: このチュートリアルでは、Azure portal 内で Azure Monitor の Log Analytics の機能を使用して、ログ クエリを作成して実行し、その結果を分析する方法について説明します。
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 06/28/2021
ms.openlocfilehash: bf19056cbde9c719c8d8665d2fc9a954e7a943b6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729340"
---
# <a name="log-analytics-tutorial"></a>Log Analytics のチュートリアル
Log Analytics は、Azure Monitor ログによって収集されたデータからログ クエリを編集して実行し、その結果を対話形式で分析する Azure portal のツールです。 Log Analytics クエリを使用すると、特定の条件に一致するレコードの取得、傾向の特定、パターンの分析を行って、データに関するさまざまな分析情報を入手できます。 

このチュートリアルでは、Log Analytics インターフェイスについて紹介し、いくつかの基本的なクエリの使用法を説明し、結果を操作する方法を示します。 次のことについて説明します。

> [!div class="checklist"]
> * ログ データのスキーマの確認
> * 単純なクエリの記述と実行、クエリの時間範囲の変更
> * クエリ結果のフィルター処理、並べ替え、グループ化
> * クエリ結果のビジュアルの表示、変更、共有
> * クエリと結果の読み込み、エクスポート、コピー

> [!IMPORTANT]
> このチュートリアルでは、Log Analytics 機能を使用して 1 つのクエリを作成し、別のサンプル クエリを使用します。 クエリの構文を学習し、クエリ自体の直接編集を開始する準備ができたら、[Kusto クエリ言語のチュートリアル](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)をお読みください。 そのチュートリアルでは、Log Analytics 内で編集して実行できるサンプル クエリについて説明しています。 このチュートリアルで学習する機能のいくつかが使用されています。


## <a name="prerequisites"></a>前提条件
このチュートリアルでは、サンプル クエリをサポートしているサンプル データが豊富に含まれている [Log Analytics のデモ環境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)を使用します。 ご自分の Azure サブスクリプションを使用することもできますが、同じテーブルにデータが含まれていない場合があります。

## <a name="open-log-analytics"></a>Log Analytics を開く
[Log Analytics のデモ環境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)を開くか、ご自分のサブスクリプションの Azure Monitor メニューから **[ログ]** を選択します。 この手順により、初期のスコープが Log Analytics ワークスペースに設定されるため、クエリではそのワークスペース内のすべてのデータから選択されます。 Azure リソースのメニューから **[ログ]** を選択した場合、スコープはそのリソースのレコードのみに設定されます。 スコープの詳細については、[ログ クエリのスコープ](./scope.md)に関するページを参照してください。

スコープは、画面の左上隅で確認できます。 独自の環境を使用している場合は、別のスコープを選択するオプションが表示されます。 このオプションはデモ環境では使用できません。

:::image type="content" source="media/log-analytics-tutorial/scope.png" alt-text="デモ用の Log Analytics スコープを示すスクリーンショット。" lightbox="media/log-analytics-tutorial/scope.png":::

## <a name="view-table-information"></a>テーブル情報の表示
画面の左側には **[テーブル]** タブがあり、現在のスコープで使用できるテーブルを調べることができます。 これらのテーブルは、既定で **ソリューション** 別にグループ化されていますが、グループを変更したり、フィルター処理したりできます。 

**ログ管理** ソリューションを展開し、**AppRequests** テーブルを見つけます。 テーブルを展開してそのスキーマを表示するか、その名前にマウス ポインターを合わせると、それに関する追加情報が表示されます。 

:::image type="content" source="media/log-analytics-tutorial/table-details.png" alt-text="テーブル ビューを示すスクリーンショット。" lightbox="media/log-analytics-tutorial/table-details.png":::

各テーブルとその列を示すテーブル参照にアクセスするには、 **[役に立つリンク]** の下のリンクを選択します。 テーブル内にある最近の数件のレコードを簡単に確認するには、 **[データのプレビュー]** を選択します。 このプレビューは、クエリを実行する前に、使用するデータが想定しているものであることを確認するのに役立ちます。

:::image type="content" source="media/log-analytics-tutorial/sample-data.png" alt-text="サンプル データを示すスクリーンショット。" lightbox="media/log-analytics-tutorial/sample-data.png":::

## <a name="write-a-query"></a>クエリを記述する
**AppRequests** テーブルを使用してクエリを記述してみましょう。 その名前をダブルクリックして、クエリ ウィンドウに追加します。 ウィンドウに直接入力することもできます。 IntelliSense を入手することもできます。これは、現在のスコープ内のテーブルの名前と Kusto クエリ言語 (KQL) コマンドを完成させるのに役立ちます。

これは、記述できる最も単純なクエリです。 テーブル内にあるすべてのレコードを返します。 これを実行するには、 **[実行]** ボタンを選択するか、クエリ テキスト内の任意の場所にカーソルを置いて Shift + Enter キーを押します。

:::image type="content" source="media/log-analytics-tutorial/query-results.png" alt-text="クエリ結果を示すスクリーンショット。" lightbox="media/log-analytics-tutorial/query-results.png":::

ご覧のように、結果が表示されます。 クエリによって返されたレコードの数が右下隅に表示されます。 

## <a name="filter-query-results"></a>クエリ結果をフィルター処理する

クエリにフィルターを追加して、返されるレコードの数を減らしてみましょう。 左側のペインにある **[フィルター]** タブを選択します。 このタブには、結果をフィルター処理するために使用できる、クエリ結果内の列が表示されます。 これらの列内の上位の値が、その値を含むレコードの数と共に表示されます。 **[ResultCode]** の下の **[200]** を選択し、 **[適用して実行]** を選択します。 

:::image type="content" source="media/log-analytics-tutorial/query-pane.png" alt-text="クエリ ペインを示すスクリーンショット。" lightbox="media/log-analytics-tutorial/query-pane.png":::

**where** ステートメントが、選択した値と共にクエリに追加されます。 結果にはその値を含むレコードだけが含まれているので、ご覧のように、レコード数が減少しています。

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-01.png" alt-text="フィルター処理されたクエリ結果を示すスクリーンショット。" lightbox="media/log-analytics-tutorial/query-results-filter-01.png":::


### <a name="time-range"></a>時間の範囲
Log Analytics ワークスペース内のすべてのテーブルには、**TimeGenerated** という名前の列があります。これは、レコードが作成された時刻です。 すべてのクエリには時間の範囲があり、結果はその範囲内の **TimeGenerated** 値を持つレコードに制限されます。 時間の範囲は、クエリ内で、または画面の上部にあるセレクターを使用して設定できます。

既定では、このクエリは過去 24 時間のレコードを返します。 すべての結果が表示されているわけではないことを示すメッセージが表示されます。 これは、Log Analytics が最大 30,000 件のレコードを返すことができ、このクエリから返されたレコードの数がそれよりも多いためです。 **[時間の範囲]** ドロップダウン リストを選択し、値を **[12 時間]** に変更します。 **[実行]** をもう一度選択すると、結果が返されます。 

:::image type="content" source="media/log-analytics-tutorial/query-results-max.png" alt-text="[時間の範囲] を示すスクリーンショット。" lightbox="media/log-analytics-tutorial/query-results-max.png":::


### <a name="multiple-query-conditions"></a>複数のクエリ条件
別のフィルター条件を追加して、結果をさらに減らしてみましょう。 クエリには、必要なレコードのセットを正確に対象とするために、任意の数のフィルターを含めることができます。 **[名前]** の下の **[Get Home/Index]** を選択し、 **[適用して実行]** を選択します。 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-02.png" alt-text="複数のフィルターを使用したクエリ結果を示すスクリーンショット。" lightbox="media/log-analytics-tutorial/query-results-filter-02.png":::


## <a name="analyze-results"></a>分析結果
Log Analytics は、クエリの記述と実行を支援するだけでなく、結果を操作するための機能も提供します。 まず、レコードを展開すると、すべての列の値が表示されます。

:::image type="content" source="media/log-analytics-tutorial/expand-record.png" alt-text="レコードの展開を示すスクリーンショット。" lightbox="media/log-analytics-tutorial/expand-record.png":::

任意の列で結果を並べ替えるには、その列の名前を選択します。 フィルターの条件を指定するには、横にあるフィルター アイコンを選択します。 これは、クエリを再度実行するとこのフィルターがクリアされる点を除いて、クエリ自体にフィルター条件を追加するのと似ています。 対話型分析の一部としてレコードのセットをすばやく分析する場合は、この方法を使用します。

たとえば、**DurationMs** 列にフィルターを設定して、**100** ミリ秒を超えるレコードに限定します。 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter.png" alt-text="クエリ結果のフィルターを示すスクリーンショット。" lightbox="media/log-analytics-tutorial/query-results-filter.png":::

結果をフィルター処理する代わりに、特定の列でレコードをグループ化することができます。 作成したフィルターをクリアしたら、 **[列のグループ化]** トグルをオンにします。 

:::image type="content" source="media/log-analytics-tutorial/query-results-group-columns.png" alt-text="[列のグループ化] をオンにしているところを示すスクリーンショット。" lightbox="media/log-analytics-tutorial/query-results-group-columns.png":::

**Url** 列をグループ化行にドラッグします。 これで、結果がその列によって整理され、各グループを折りたたんで分析しやすくすることができます。

:::image type="content" source="media/log-analytics-tutorial/query-results-grouped.png" alt-text="グループ化されたクエリ結果を示すスクリーンショット。" lightbox="media/log-analytics-tutorial/query-results-grouped.png":::

## <a name="work-with-charts"></a>グラフの操作
グラフで表示できる数値データを使用するクエリを見てみましょう。 クエリを作成する代わりに、クエリの例を選択します。

左側のペインで、 **[クエリ]** を選択します。 このペインには、クエリ ウィンドウに追加できるクエリの例が含まれています。 独自のワークスペースを使用している場合は、複数のカテゴリにさまざまなクエリが表示されます。 デモ環境を使用している場合は、1 つの **[Log Analytics ワークスペース]** カテゴリがのみが表示されます。 それを展開して、カテゴリ内のクエリを表示します。

**[アプリケーション]** カテゴリの **[Function Error rate]\(関数エラー率\)** というクエリを選択します。 この手順により、そのクエリがクエリ ウィンドウに追加されます。 新しいクエリは他と空白行で区切られることに注意してください。 KQL のクエリは空白行を検出すると終了します。そのため、これらは別個のクエリとして認識されます。 

:::image type="content" source="media/log-analytics-tutorial/example-query.png" alt-text="新しいクエリを示すスクリーンショット。" lightbox="media/log-analytics-tutorial/example-query.png":::

カーソルが配置されているのが現在のクエリです。 ご覧のように、先頭のクエリが強調表示され、それが現在のクエリであることを示しています。 新しいクエリ内の任意の場所をクリックして選択し、 **[実行]** ボタンを選択して実行します。

:::image type="content" source="media/log-analytics-tutorial/example-query-output-table.png" alt-text="クエリ結果テーブルを示すスクリーンショット。" lightbox="media/log-analytics-tutorial/example-query-output-table.png":::

結果をグラフで表示するには、結果ペインで **[グラフ]** を選択します。  別の種類への変更など、グラフを操作するためのさまざまなオプションがあることに注意してください。

:::image type="content" source="media/log-analytics-tutorial/example-query-output-chart.png" alt-text="クエリ結果のグラフを示すスクリーンショット。" lightbox="media/log-analytics-tutorial/example-query-output-chart.png":::


## <a name="next-steps"></a>次のステップ

Log Analytics の使用方法を学んだので、次はログ クエリの使用に関するチュートリアルを完了してください。
> [!div class="nextstepaction"]
> [Azure Monitor ログ クエリを記述する](get-started-queries.md)
