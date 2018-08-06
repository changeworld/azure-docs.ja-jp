---
title: 収集された Azure Log Analytics データの表示または分析 | Microsoft Docs
description: この記事のチュートリアルでは、ログ検索ポータルを使用してログ検索を作成し、Log Analytics リソースに保存されたデータを分析する方法を説明します。  このチュートリアルでは、さまざまな種類のデータを返すシンプルなクエリを実行し、結果を分析します。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/31/2018
ms.author: magoedte
ms.custom: mvc
ms.component: na
ms.openlocfilehash: 31e9e6b173a578b09f656850271ed5a8f0f2baa8
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391333"
---
# <a name="view-or-analyze-data-collected-with-log-analytics-log-search"></a>Log Analytics のログ検索で収集されたデータの表示または分析

Log Analytics では、収集されたデータを分析するクエリを構築してログ検索を利用できます。また、よく利用する検索のグラフィカルな表示を使用してカスタマイズした既存のダッシュボードを利用することもできます。  Azure VM とアクティビティ ログから運用データのコレクションを定義したので、このチュートリアルでは次の方法について説明します。

> [!div class="checklist"]
> * イベント データの単純な検索を実行し、結果を変更およびフィルターする機能を使用する 
> * パフォーマンス データを処理する方法を学ぶ

このチュートリアルの例を完了するには、[Log Analytics ワークスペースに接続された](log-analytics-quick-collect-azurevm.md)既存の仮想マシンが必要です。  

クエリの作成と編集および返されたデータの対話的操作は、2 つの方法のいずれかで行うことができます。  基本的なクエリの場合は Azure Portal の [ログ検索] ページを使い、高度なクエリの場合は高度な分析ポータルを使うことができます。 2 つのポータルの機能の違いについて詳しくは、「[Azure Log Analytics でログ クエリを作成および編集するためのポータル](log-analytics-log-search-portals.md)」をご覧ください。

このチュートリアルでは、Azure Portal の [ログ検索] を使います。 

## <a name="log-in-to-azure-portal"></a>Azure Portal へのログイン
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインします。 

## <a name="open-the-log-search-portal"></a>ログ検索ポータルを開く 
最初に、ログ検索ポータルを開きます。   

1. Azure Portal で、**[すべてのサービス]** をクリックします。 リソースの一覧で「**Monitor**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[モニター]** を選択します。
2. モニターのナビゲーション メニューで **[ログの分析]** を選択し、ワークスペースを選択します。

## <a name="create-a-simple-search"></a>シンプルな検索を作成する
テーブルのすべてのレコードを返すシンプルなクエリを使用すると、作業データを最も簡単に取得できます。  Windows クライアントまたは Linux クライアントがワークスペースに接続されている場合、データは Event (Windows) テーブルまたは Syslog (Linux) テーブルにあります。

検索ボックスに次のいずれかのクエリを入力し、検索ボタンをクリックします。  

```
Event
```
```
Syslog
```

データは既定のリスト ビューに返されます。ここで、返されたレコードの合計数を確認できます。

![単純なクエリ](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-01.png)

表示されているのは、各レコードの最初の数個のプロパティだけです。  特定のレコードのすべてのプロパティを表示するには、**[show more]\(詳細を表示\)** をクリックします。

## <a name="filter-results-of-the-query"></a>クエリのフィルター結果
画面の左側はフィルター ウィンドウです。ここでは、クエリを直接変更せずに、フィルターを追加することができます。  そのレコードの種類について、レコードのプロパティがいくつか表示されます。1 つまたは複数のプロパティ値を選択して検索結果を絞り込むことができます。

**Event** を操作する場合は、**[EVENTLEVELNAME]** の **[Error]** の横にあるチェック ボックスをオンにします。   **Syslog** を操作する場合は、**[SEVERITYLEVEL]** の **[err]** の横にあるチェック ボックスをオンにします。  これにより、クエリが次のいずれかの変更され、結果がエラー イベントに制限されます。

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![filter](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-02.png)

レコードのいずれかのプロパティ メニューから **[フィルターに追加]** を選択して、プロパティをフィルター ウィンドウに追加します。

![[フィルターに追加] メニュー](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-03.png)

同じフィルターを設定するには、フィルターを適用する値を持つレコードのプロパティ メニューから **[フィルター]** を選択します。  

マウスを移動すると名前が青色で表示されるプロパティには、**[フィルター]** オプションのみがあります。  これは、検索条件のインデックスが付けられた "*検索可能*" フィールドです。  灰色のフィールドは "*フリー テキスト検索可能*" フィールドで、**[参照設定を表示]** オプションのみがあります。  このオプションは、その値を持つ任意のプロパティ レコードを返します。

1 つのプロパティで結果をグループ化するには、レコード メニューで **[グループ化]** オプションを選択します。  これにより、グラフの結果を表示する [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 演算子がクエリに追加されます。  複数のプロパティでグループ化できますが、クエリを直接編集する必要があります。  **Computer** プロパティの横にあるレコード メニューを選択し、**['Computer' でグループ化]** を選択します。  

![コンピューターでグループ化](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-04.png)

## <a name="work-with-results"></a>結果を操作する
ログ検索ポータルには、クエリの結果を操作するためのさまざまな機能が用意されています。  結果の並べ替え、フィルター処理、およびグループ化機能を使用すると、実際のクエリを変更せずに、データを分析できます。  既定では、クエリの結果は並べ替えられません。

フィルター処理および並べ替えの追加オプションが用意されたテーブル フォームでデータを表示するには、**[テーブル]** をクリックします。  

![テーブル ビュー](media/log-analytics-tutorial-viewdata/log-search-portal-table-01.png)

レコードの矢印をクリックすると、そのレコードの詳細が表示されます。

![結果の並べ替え](media/log-analytics-tutorial-viewdata/log-search-portal-table-02.png)

フィールドを並べ替えるには、そのフィールドの列ヘッダーをクリックします。

![結果の並べ替え](media/log-analytics-tutorial-viewdata/log-search-portal-table-03.png)

列の特定の値に基づいて結果をフィルター処理するには、フィルター ボタンをクリックして、フィルター条件を指定します。

![結果のフィルター処理](media/log-analytics-tutorial-viewdata/log-search-portal-table-04.png)

列でグループ化するには、その列ヘッダーを、結果の最上部にドラッグします。  複数の列を最上部にドラッグすると、複数のフィールドでグループ化できます。

![結果のグループ化](media/log-analytics-tutorial-viewdata/log-search-portal-table-05.png)


## <a name="work-with-performance-data"></a>パフォーマンス データを処理する
Log Analytics ワークスペースの **Perf** テーブルには、Windows エージェントと Linux エージェントの両方のパフォーマンス データが格納されています。  パフォーマンス レコードは他のレコードと同じように見えるため、イベントと同様に、すべてのパフォーマンス レコードを返すシンプルなクエリを記述します。

```
Perf
```

![パフォーマンス データ](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-01.png)

ただし、すべてのパフォーマンス オブジェクトとカウンターの何百万ものレコードを返しても、それほど役には立ちません。  上記で使用したのと同じ方法を使用すると、データをフィルター処理できます。また、ログ検索ボックスに、次のクエリを直接入力することもできます。  これにより Windows と Linux の両方のコンピューターのプロセッサ使用率レコードのみが返されます。

```
Perf | where ObjectName == "Processor"  | where CounterName == "% Processor Time"
```

![プロセッサ使用率](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-02.png)

これによりデータが特定のカウンターに制限されますが、特に便利なフォームには、まだデータが入力されません。  データは折れ線グラフに表示できますが、最初に Computer と TimeGenerated でデータをグループ化する必要があります。  複数のフィールドでグループ化するには、クエリを直接変更する必要があるため、次のようにクエリを変更します。  これにより、**CounterValue** プロパティで [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) 関数が使用され、1 時間ごとに平均値が計算されます。

```
Perf  
| where ObjectName == "Processor"  | where CounterName == "% Processor Time"
| summarize avg(CounterValue) by Computer, TimeGenerated
```

![パフォーマンス データのグラフ](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-03.png)

データが適切にグループ化されたので、[render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) 演算子を追加して、データをビジュアル グラフに表示できます。  

```
Perf  
| where ObjectName == "Processor" | where CounterName == "% Processor Time" 
| summarize avg(CounterValue) by Computer, TimeGenerated 
| render timechart
```

![折れ線グラフ](media/log-analytics-tutorial-viewdata/log-analytics-portal-linechart-01.png)

## <a name="next-steps"></a>次の手順
このチュートリアルでは、イベントおよびパフォーマンス データを分析する基本的なログ検索を作成する方法について説明しました。  次のチュートリアルでは、ダッシュボードを作成して、データを視覚化する方法について説明します。

> [!div class="nextstepaction"]
> [Log Analytics データのダッシュボードを作成して共有する](log-analytics-tutorial-dashboards.md)
