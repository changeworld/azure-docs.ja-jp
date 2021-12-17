---
title: Azure Monitor でログ クエリの使用を開始する | Microsoft Docs
description: この記事では、Azure Monitor でログ クエリの記述を開始するためのチュートリアルを提供します。
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/20/2021
ms.openlocfilehash: b7ac389da66d68cd0fdddf15b46ddcee6ff537f6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248251"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Azure Monitor でログ クエリの使用を開始する

> [!NOTE]
> 少なくとも 1 つの仮想マシンからデータを収集する場合は、独自の環境でこの演習を行うことができます。 その他のシナリオでは、サンプル データが多数含まれている Microsoft の[デモ環境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)を使用してください。  
>
> Kusto クエリ言語でクエリを実行する方法は既にわかっているが、リソースの種類に基づいて便利なクエリをすばやく作成する必要がある場合は [Azure Monitor Log Analytics でのクエリの使用](../logs/queries.md)に関する記事の保存済みサンプル クエリ ペインを参照してください。

このチュートリアルでは、Azure Monitor でログ クエリを記述する方法について説明します。 この記事では、次の方法について説明します。

- クエリの構造の概要。
- クエリ結果の並べ替え。
- クエリ結果のフィルター処理。
- 時間の範囲の指定。
- 結果に含めるフィールドの選択。
- カスタム フィールドの定義と使用。
- 結果の集計とグループ化。

Azure portal での Log Analytics の使用に関するチュートリアルについては、[Azure Monitor Log Analytics の使用開始](./log-analytics-tutorial.md)に関するページを参照してください。

Azure Monitor でのログ クエリの詳細については、[Azure Monitor でのログ クエリの概要](../logs/log-query-overview.md)に関するページをご覧ください。

このチュートリアルの動画バージョンをご覧ください。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="write-a-new-query"></a>新しいクエリを作成する

クエリは、テーブル名または *search* コマンドから始めることができます。 まずテーブル名から始めることをお勧めします。これにより、クエリの明確な範囲が定義され、クエリのパフォーマンスと結果の関連性の両方が向上するためです。

> [!NOTE]
> Azure Monitor によって使用される Kusto クエリ言語では、大文字と小文字が区別されます。 通常、言語のキーワードは小文字で記述されます。 クエリ内でテーブルまたは列の名前を使用する場合は、スキーマ ペインの表示に従って、必ず大文字と小文字を正しく指定してください。

### <a name="table-based-queries"></a>テーブルベースのクエリ

Azure Monitor では、ログ データはテーブルに編成され、各テーブルは複数の列で構成されます。 Analytics ポータルでは、すべてのテーブルと列は Log Analytics のスキーマ ウィンドウに表示されます。 目的のテーブルを指定し、データの一部を見てみましょう。

```Kusto
SecurityEvent
| take 10
```

上記のクエリは、順不同で *SecurityEvent* テーブルから 10 件の結果を返します。 これは、テーブルの概要を把握し、その構造と内容を理解するための一般的な方法です。 それでは、どのように構築されているかを見てみましょう。

* クエリは、クエリの範囲を定義するテーブル名 *SecurityEvent* で始まります。
* パイプ (|) 文字で複数のコマンドを区切ると、最初のコマンドの出力が次の入力になります。 パイプでつないだ要素は、任意の数を追加できます。
* パイプの後には **take** コマンドがあります。このコマンドは、テーブルから特定の数の任意のレコードを返します。

`| take 10` を追加せずにクエリを実際に実行することもできます。 このコマンドは引き続き有効ですが、最大で 10,000 件の結果を返す可能性があります。

### <a name="search-queries"></a>検索クエリ

検索クエリはあまり構造化されていないため、一般的に、任意の列に特定の値が含まれるレコードを検索する場合により適しています。

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

このクエリで、*SecurityEvent* テーブルの "Cryptographic" というフレーズが含まれるレコードが検索されます。 このようなレコードの中から、10 個のレコードが返され、表示されます。 `in (SecurityEvent)` の部分を省略して `search "Cryptographic"` のみを実行すると、"*すべての*" テーブルに対して検索が実行されます。これには時間がかかり、効率が良くありません。

> [!IMPORTANT]
> 検索クエリは通常、処理する必要のあるデータが増えるため、テーブル ベースのクエリより低速です。 

## <a name="sort-and-top"></a>sort と top
**take** は少数のレコードを取得する場合に便利ですが、結果は順不同で選択され、表示されます。 順序が設定されたビューを取得するには、優先する列で **並べ替え** ます。

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

ただし、前述のクエリでは返される結果が多すぎる場合や、処理に時間もかかる場合があります。 このクエリで、SecurityEvent テーブル"*全体*"が **TimeGenerated** 列で並べ替えられます。 また、Analytics ポータルでは、表示が 10,000 レコードに制限されます。 Analytics ポータルでは、表示が 10,000 レコードに制限されます。

最新の 10 レコードのみを取得するには、**top** を使用する方法が最適です。サーバー側でテーブル全体が並べ替えられてから、上位のレコードが返されます。

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

降順が既定の並べ替え順序です。そのため、通常は **desc** の引数を省略します。 出力は次のようになります。

![上位 10 件のレコードを降順に並べ替えたスクリーンショット。](media/get-started-queries/top10.png)


## <a name="the-where-operator-filtering-on-a-condition"></a>where 演算子: 条件に基づいたフィルター処理
フィルターは、その名前のとおり、特定の条件に基づいてデータをフィルター処理します。 これは、クエリ結果を関連する情報に制限する最も一般的な方法です。

クエリにフィルターを追加するには、**where** 演算子に続けて 1 つまたは複数の条件を使用します。 たとえば、次のクエリは、_Level_ が _8_ に等しい *SecurityEvent* レコードのみを返します。

```Kusto
SecurityEvent
| where Level == 8
```

フィルター条件を記述する場合は、次の式を使用できます。

| 式 | 説明 | 例 |
|:---|:---|:---|
| == | 等価性をチェックします<br>(大文字と小文字を区別します) | `Level == 8` |
| =~ | 等価性をチェックします<br>(大文字と小文字は区別されません) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=、<> | 非等価性をチェックします<br>(両方の式は同じです) | `Level != 4` |
| *and*、*or* | 条件の間に必要です| `Level == 16 or CommandLine != ""` |

複数の条件でフィルター処理するには、次のいずれかの方法を使用できます。

次に示すように、**and** を使用します。

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

次に示すように、複数の **where** 要素を順番にパイプでつなぎます。

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> 値の型は異なる可能性があるため、正しい型に基づいて比較を実行するために型をキャストする必要がある場合があります。 たとえば、SecurityEvent *Level* 列は String 型なので、数値演算子を使用する前に、次に示すように *int* または *long* などの数値型にキャストする必要があります。`SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>時間の範囲を指定する

### <a name="use-the-time-picker"></a>日時ピッカーを使用する

日時ピッカーは **[実行]** ボタンの横に表示され、過去 24 時間のレコードのみのクエリを実行していることを示しています。 これは、すべてのクエリに適用される既定の時間範囲です。 過去 1 時間のレコードのみを取得するには、 _[過去 1 時間]_ を選択してから、クエリを再度実行します。

![日時ピッカーとその時間範囲コマンドの一覧のスクリーンショット。](media/get-started-queries/timepicker.png)


### <a name="add-a-time-filter-to-the-query"></a>クエリに時間フィルターを追加する

また、時間フィルターをクエリに追加して、独自の時間の範囲を定義することもできます。 テーブル名の直後に時間フィルターを配置することをお勧めします。 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

上記の時間フィルターでは、`ago(30m)` は "30 分前" を意味します。つまり、このクエリは過去 30 分間 (30m のように表記) のレコードのみを返します。 その他の時間単位には、日 (2d など) や秒 (10s など) があります。


## <a name="use-project-and-extend-to-select-and-compute-columns"></a>project と extend を使用して列を選択および計算する

結果に含める特定の列を選択するには、**project** を使用します。

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

上記の例では、次の出力が生成されます。

![クエリ "project" の結果一覧のスクリーンショット。](media/get-started-queries/project.png)

また、**project** を使用して列の名前を変更し、新しい列を定義することもできます。 次の例では、**project** を使用して以下を実行します。

* 元の列の *Computer* および *TimeGenerated* のみを選択します。
* *Activity* 列を *EventDetails* として表示します。
* *EventCode* という名前の新しい列を作成します。 **substring ()** 関数は、Activity フィールドの先頭の 4 文字のみを取得するために使用されます。


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**extend** を使用すると、元の列をすべて結果セットに保持し、追加のものを定義できます。 次のクエリでは、**extend** を使用して、*EventCode* 列が追加されています。 この列は、テーブル結果の最後に表示されないことがあります。その場合、これを表示するには、レコードの詳細を展開する必要があります。

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="use-summarize-to-aggregate-groups-of-rows"></a>summarize を使用して行のグループを集計する
**summarize** を使用して、1 つまたは複数の列に従ってレコードのグループを特定し、それらに集計を適用します。 **summarize** の最も一般的な用途は、各グループの結果の数を返す *count* です。

次のクエリでは、過去 1 時間のすべての *Perf* レコードを確認し、*ObjectName* でグループ化し、各グループのレコードをカウントします。 

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

複数の次元でグループを定義することが理にかなっている場合があります。 これらの値の一意の組み合わせで、それぞれ別のグループが定義されます。

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

もう 1 つの一般的な用途は、各グループに対して数学的または統計的計算を実行する場合です。 次の例では、各コンピューターの平均 *CounterValue* を計算します。

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

残念ながら、さまざまなパフォーマンス カウンターが混在するので、このクエリの結果は意味がありません。 より意味のある結果にするために、*CounterName* と *Computer* の組み合わせごとに個別に平均を計算します。

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>時間列で要約する
時間列または別の連続する値に基づいて結果をグループ化することもできます。 ただし、これらは一意の値なので、`by TimeGenerated` を要約するだけでは、時間の範囲全体に 1 ミリ秒ごとのグループが作成されます。 

連続する値に基づくグループを作成するには、**bin** を使用して、管理しやすい単位に範囲を分割することをお勧めします。 次のクエリでは、特定のコンピューター上の空きメモリ ( *[利用可能な MB]* ) を測定する *Perf* レコードを分析します。 過去 7 日間の 1 時間ごとの平均値が計算されます。

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

出力をよりわかりやすくするために、時系列で空きメモリを示す時間グラフとして表示するように選択できます。

![クエリ メモリの値を時系列で表示するスクリーンショット。](media/get-started-queries/chart.png)



## <a name="next-steps"></a>次の手順

- ログ クエリでの文字列データの使用の詳細について、[Azure Monitor ログ クエリでの文字列の操作](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)に関するページをご覧ください。
- ログ クエリでのデータの集計の詳細について、[Azure Monitor ログ クエリでの高度な集計](/azure/data-explorer/write-queries#advanced-aggregations)に関するページをご覧ください。
- 複数のテーブルのデータを結合する方法について、[Azure Monitor ログ クエリでの結合](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins)に関するページをご覧ください。
- Kusto クエリ言語全体のドキュメントについては、[KQL 言語リファレンス](/azure/kusto/query/)を参照してください。
