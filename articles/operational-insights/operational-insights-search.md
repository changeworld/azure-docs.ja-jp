<properties
   pageTitle="Operational Insights でデータを検索する"
   description="Microsoft Azure Operational Insights のログ検索を使用して、探しているデータを見つけることができます。"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/21/2015"
   ms.author="banders" />

# Operational Insights でデータを検索する

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft Azure Operational Insights の中心にあるのは、環境内の複数のソースのマシン データを組み合わせて相関させることができるログ検索機能です。各種のソリューションも、特定の問題領域に関するj測定基準を得るためのログ検索機能を備えています。

検索クエリは [検索] ページで作成できます。検索結果は、ファセット コントロールを使用してフィルタリングすることができます。検索結果に対して変換やフィルター処理、レポート作成などを実行する高度なクエリを作成することもできます。

ほとんどのソリューション ページに、一般的な検索クエリが表示されます。Operational Insights コンソールで、タイルをクリックするか他の項目にドリル インし、ログ検索を使用して項目の詳細を表示できます。

このチュートリアルでは、ログ検索を使用するときの基礎について、そのすべてを例を通じて説明します。

単純で実用的な例から始めて、実際的な使用事例をあげながら、データから洞察を引き出すために構文をどのように使用するかを理解できるように説明を進めていきます。

Operational Insights でログ検索を行うときは、以下のテクニックを使用します。

- 基本的なフィルターを使用する
- 追加フィルターを使用する
- 検索結果を操作する
- Measure コマンドを使用する
- Measure コマンドで Max 関数と Min 関数を使用する
- Measure コマンドで Avg 関数を使用する
- Where コマンドを使用する

検索テクニックを十分に理解した後、「[検索構文リファレンス](#search-syntax-reference)」と「[検索フィールドとファセットのリファレンス](#search-field-and-facet-reference)」を参照できます。

## 基本的なフィルターを使用する

最初に知るべきことは検索クエリの先頭部分、つまり "|" 縦棒パイプ文字の前は常に "フィルター" であることです。それは TSQL での WHERE 句と考えることができ、Operational Insights データ ストアから引き出すデータのサブセットは "何か" を決定します。データ ストアの検索は、ほとんどが抽出するデータの特性を指定することに関係するため、クエリが WHERE 句から始まることは自然なことです。

使用できる最も基本的なフィルターは、'error'、'timeout'、コンピューター名などの "キーワード" です。通常、この種の単純なクエリでは、同じ結果セットの中にさまざまな形のデータが返ります。Operational Insights がシステム内に異なる "種類" のデータを持っているのはこのためです。


### 単純な検索を実行するには
1. Operational Insights ポータルで、**[検索データ エクスプローラー]** をクリックします。![検索タイル](./media/operational-insights-search/overview-search.png)
2. クエリ フィールドに 「`error`」と入力し、**[検索]** をクリックします。![error の検索](./media/operational-insights-search/search-error.png) たとえば、次の図に示す `error` のクエリでは、100,000 件の**Event** レコード (Log Management が収集)、18 件の **ConfigurationAlert** レコード (Configuration Assessment が生成)、12 件の **ConfigurationChange** レコード (Change Tracking がキャプチャ) が返っています。![検索結果](./media/operational-insights-search/results01.png)

これらのフィルターは実際はオブジェクト型でもクラスでもありません。"Type" は、データに付けられた、単なるタグ、プロパティ、または文字列/名前/カテゴリです。システム内のドキュメントには、**Type:ConfigurationAlert** とタグ付けされたものや **Type:PerfHourly**、**Type:Event** などとタグ付けされたものがあります。それぞれの検索結果、ドキュメント、レコード、またはエントリには、各データの生のプロパティとその値がすべて表示されます。特定のフィールドに特定の値があるレコードのみを取得する場合は、フィルター内にフィールド名を指定できます。

"Type" は、実際はすべてのレコードが持っているフィールドであり、他のフィールドと異なっている点はありません。これは Type フィールドの値に基づいて設定されました。そのレコードは、さまざまな形になるでしょう。ちなみに、**Type=PerfHourly** や **Type=Event** も、時間当たりのパフォーマンス データの集計やイベントをクエリするために学習する必要がある構文です。

フィールド名の後と値の前に、コロン (:) または等号 (=) のいずれかを使用できます。**Type:Event** と **Type=Event** は意味は同じであり、好みのスタイルを選択できます。

したがって、Type=PerfHourly レコードに 'CounterName' というフィールドがある場合は、`Type=PerfHourly CounterName="% Processor Time"` のようなクエリを記述できます。

このクエリでは、パフォーマンス カウンターの名前が "% Processor Time" であるパフォーマンス データだけが返ります。

### プロセッサ時間のパフォーマンス データを検索するには
- 検索クエリ フィールドに、「`Type=PerfHourly CounterName="% Processor Time"`」と入力します。

もっと詳しく指定することもできます。クエリで **InstanceName=\_'Total'** を使用します。これは Windows パフォーマンス カウンターです。ファセットや別の **field:value** を選択することもできます。フィルターは、クエリ バー内のフィルターに自動的に追加されます。これを次の図で確認できます。図は、何も入力せずに **InstanceName:’\_Total’** をクエリに追加するためにクリックする場所を示しています。

![ファセットの検索](./media/operational-insights-search/search-facet.png)

これで、クエリは `Type=PerfHourly CounterName=”% Processor Time” InstanceName=”_Total”` になります。

この例では、結果を取得するために **Type=PerfHourly** を指定する必要はありません。CounterName フィールドと InstanceName フィールドは Type=PerfHourly のレコードにのみ存在すため、このクエリの結果は ```
CounterName=”% Processor Time” InstanceName=”_Total”
``` と同じになります。

これは、クエリ内のすべてのフィルターは *AND* として評価されるためです。条件にフィールドを追加すればするほど、結果は少なくなり、より詳しく、より洗練されていきます。

たとえば、`Type=Event EventLog="Windows PowerShell"` というクエリは `Type=Event AND EventLog="Windows PowerShell"` と同じです。それは、Windows PowerShell イベント ログに記録され、収集されたすべてのイベントを返します。同じファセットを繰り返し選択することで 1 つのフィルターを複数回追加しても、問題は見た目の問題だけです。検索バーが読み取りにくくなりますが、暗黙的な AND 演算子は常に存在するため、同じ結果が返ります。

暗黙的な AND 演算子は NOT 演算子を明示的に使用することで簡単に逆転させることができます。次に例を示します。

`Type:Event NOT(EventLog:"Windows PowerShell")` またはそれと同等である `Type=Event EventLog!="Windows PowerShell"` は、Windows PowerShell ログではない (NOT) 他のすべてのログからすべてのイベントを返します。

または、'OR' などの他のブール演算子を使用できます。次のクエリは、EventLog が Application または (OR) System であるレコードを返します。

```
EventLog=Application OR EventLog=System
```

上のクエリを使用すると、同じ結果セットの中に両方のログのエントリが取得されます。

ただし、暗黙的な AND を残したまま OR を削除した場合、両方のログに属すイベント ログは存在しないため、次のクエリでは何の結果も返りません。イベント ログのエントリは、2 つのログのどちらかのみに書き込まれていました。

```
EventLog=Application EventLog=System
```


## 追加フィルターを使用する

次のクエリは、データを送信しているすべてのコンピューターの 2 つのイベント ログのエントリを返します。

```
EventLog=Application OR EventLog=System
```

![検索結果](./media/operational-insights-search/search-results03.png)

フィールドまたはフィルターのいずれかを選択するとクエリが特定のコンピューターに絞り込まれ、その他のすべてのコンピューターが除外されます。クエリは次のようになります。

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

これは暗黙的な ANDのため、次と同じです。

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

各クエリは、次の明示的な順序で評価されます。かっこに注意してください。

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

イベント ログのフィールドと同じように、OR を追加することで、特定のコンピューターのデータだけを取得できます。次に例を示します。

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

同様に、次のクエリは、選択した 2 台のコンピューターの **CPU 時間の割合**を返します。

```
CounterName=”% Processor Time”  AND InstanceName=”_Total” AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```


### ブール演算子
日時フィールドと数値フィールドでは、"より大きい"、"未満"、および "以上" を使用して値を検索できます。クエリ検索バーで、>、<、>=、<=、!= などの単純な演算子を使用できます。


特定の期間の特定のイベント ログをクエリできます。たとえば、過去 24 時間は、次のニーモニック式で表されます。

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### ブール演算子を使用して検索するには
- 検索クエリ フィールドに「`EventLog=System TimeGenerated>NOW-24HOURS"` ![ブール演算子を使用した検索](./media/operational-insights-search/search-boolean.png)」と入力します。

期間は視覚的に制御でき、ほとんどの場合そうしたいと思いますが、時間フィルターを直接クエリに含めることには利点があります。たとえば、これはダッシュボードで非常に役立ちます。ダッシュボードでは、ダッシュボード ページの"グローバル" なタイムセレクターに関係なく、各タイルの時間をオーバーライドできます。詳細については、「[ダッシュボードでの時間の問題](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/)」を参照してください。

時間でフィルター処理するときは、2 つの期間 (Operational Insights ポータルで指定した期間 (S1) とクエリに指定した期間 (S2)) の*共通部分*が結果として取得されることに注意してください。

![共通部分](./media/operational-insights-search/intersection.png)

つまり、期間に共通部分がない場合 (たとえば Operational Insights ポータルで **[今週]** を選択し、クエリに **[先週]** を定義した場合)、共通部分がないため、何の結果も取得されません。

TimeGenerated フィールドで使用される比較演算子は、他の状況でも役立ちます。たとえば、数値フィールドでも使用できます。

例として、Configuration Assessment のアラートに以下の重大度の値があるとします。

- 0 = 情報
- 1 = 警告
- 2 = 重大

次のクエリを使用して、警告アラートと重大アラートの両方をクエリすると同時に、情報アラートを除外できます。

```
Type=ConfigurationAlert  Severity>=1
```


範囲クエリを使用することもできます。これは、連続する値の先頭と末尾の範囲を指定できることを意味します。たとえば、Operations Manager イベント ログから、イベント ID が 2100 以上で 2199 未満のイベントを取得する場合は、次のクエリを使用すると、それらが戻ります。

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


>[AZURE.NOTE]使用する必要がある範囲構文は、コロン (:) 区切り記号であり (field:value)、等号 (=) は使用しません。範囲の下限と上限を 2 つのピリオド(..) で区切り、全体を角かっこで囲みます。

## 検索結果を操作する

データを検索しているときに、検索クエリを調整して結果を適切なレベルに制御できます。結果が取得されたら、コマンドを適用して結果を変換できます。

Operational Insights 検索のコマンドは、縦棒パイプ文字 (|) の後ろに指定する必要があります。クエリ文字列の最初の部分は、常にフィルターにする必要があります。それは、操作するデータ セットを定義した後、結果をコマンドに "パイプ" します。このパイプを使用して、その他のコマンドを追加できます。これは、大まかに言って Windows PowerShell パイプラインに似ています。

全体として、Operational Insights の検索言語は、IT プロフェッショナルが慣れている PowerShell のスタイルとガイドラインに従うことで、容易に学習できるようになっています。

コマンドには動詞の名前が付いているため、そのコマンドが何をするかを簡単に識別できます。

### 並べ替え

Sort コマンドは、1 つまたは複数のフィールドによって並べ替え順序を定義できるようにします。このコマンドを使用しない場合でも、時間による降順が既定によって適用されます。最新の結果が常に検索結果の最上位に表示されます。つまり、`Type=Event EventID=1234` を使用して検索を実行すると、実際には次のクエリが実行されます。

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

これは、ログでよく見られる種類の動作であり、たとえば Windows イベント ビューアーが該当します。

Sort を使用して、結果が返される方法を変更できます。この操作の例を以下に示します。

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


上記の単純な例は、コマンドの動作方法を示しています。これらのコマンドはフィルターが返した結果の形を変更します。

### Limit と Top
あまり知られていないコマンドとして Limit があります。Limit は PowerShellに似た動詞です。Limit の機能は Top コマンドと同じです。以下のクエリは同じ結果を返します。

```
Type=Event EventID=2110 | Limit 1
```

```
Type=Event EventID=2110 | Top 1
```


#### Top を使用して検索するには
- 検索クエリ フィールドに「`Type=Event EventID=2110 | Top 1` ![Top を使用した検索](./media/operational-insights-search/search-top.png)」と入力します。

上記の図では、EventID が 2110 であるレコードが 988 個あります。左側のフィールド、ファセット、およびフィルターには、クエリのパイプ文字の前の部分である "フィルター部分によって" 返された結果についての情報が常に表示されます。 **[結果]** パネルには最新の結果 が 1 つだけ返されます。これは、コマンドによって結果が成形され、変換されたためです。

### [

Select コマンドは、PowerShell の Select-Object のように動作します。それはフィルター処理された結果を返し、結果に元のプロパティがすべて含まれることはありません。代わりに、指定したプロパティのみが選択されます。

#### Select コマンドを使用して検索を実行するには

1. [検索] で「`Type=Event`」と入力し、**[検索]** をクリックします。
2. 結果が持っているすべてのプロパティを表示するには、結果のいずれかで **[+ 詳細]** をクリックします。
3. その中の一部を明示的に選択します。クエリが `Type=Event | Select Computer,EventID,RenderedDescription` に変わります。![Select を使用した検索](./media/operational-insights-search/search-select.png)

これは、検索の出力を管理し、探索に本当に関係するデータ部分を選択する場合に特に役立つコマンドです。多くの場合、レコード全体が探索に関係することはありません。このコマンドは、異なる種類のレコードの中に共通するプロパティが "いくつか" あるが、"すべての" プロパティが共通しているわけではない場合にも便利です。その後、テーブルなどのもっと自然な形での出力や、CSV ファイルにエクスポートして Excel で表示すると理解しやすい出力を生成できます。

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

## Measure コマンドを使用する

Measure は、Operational Insights 検索で最も用途の広いコマンドの 1 つです。データに統計*関数*を適用し、結果を指定したフィールドでグループ化して集計できます。Measure がサポートする統計関数は複数あります。

### Measure count()

操作する最初の統計関数であり、最も簡単な関数の 1 つは *count()* 関数です。

`Type=Event` などの検索クエリの結果では、検索結果の左側にフィルター (ファセットとも呼ばれます) が表示されます。これらのフィルターは、実行された検索の結果を、特定のフィールドによる値の分布で示します。

![Measure count() を使用した検索](./media/operational-insights-search/search-measure-count01.png)

たとえば、上の図では、**Computer** フィールドがあり、およそ 300 万件のイベントが結果に含まれ、これらのレコード内の **Computer** フィールドに一意の値が 20 個含まれていることを示しています。タイルには、上位 5 つの値のみが表示されています。これらは、**Computer** フィールドに書き込まれた最も共通している 5 つの値であり、そのフィールドに特定の値を含むドキュメントの数によって並べ替えられています。この図から、およそ 300 万件のイベントの中の 88 万件が DM コンピューター、60 万 2 千件が DE コンピューターのものであることがわかります。


タイルには上位 5 件のみが表示されていますが、すべての値を確認するにはどうすればよいでしょうか?

それが Measure コマンドの count() 関数を使用して実行できることです。この関数はパラメーターを使用しません。グループ化するフィールドを指定するだけです。ここでは **Computer** フィールドを指定します。

`Type=Event | Measure count() by Computer`

![Measure count() を使用した検索](./media/operational-insights-search/search-measure-count-computer.png)

ただし、**Computer** は単に各データの*中で*使用されるフィールドであり、関連するリレーショナル データベースはなく、独立した **Computer** オブジェクトも存在しません。データの*中の*値だけが、値を生成したエンティティと、データの他の特性と側面を記述できます。*ファセット*という用語が使用されるのはこのためです。ただし、その他のフィールドでも同じようにグループ化できます。Measure コマンドにパイプされるおよそ 300 万件のイベントという元の結果には、**EventID** という名前のフィールドも含まれているため、同じテクニックを適用してそのフィールドでグループ化することで、EventID 別のイベント数を取得できます。

```
Type=Event | Measure count() by EventID
```

特定の値を含む実際のレコードの数に関心はなく、値そのものの一覧が欲しい場合は、*Select* コマンドをクエリの末尾に追加し、最初の列を選択できます。

```
Type=Event | Measure count() by EventID | Select EventID
```

次に、複雑なクエリの結果を事前に並べ替えて取得できます。並べ替えはグリッド内の列をクリックすることでも実現できます。

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### Measure count() を使用して検索するには

- 検索クエリ フィールドに、「`Type=Event | Measure count() by EventID`」と入力します。
- `| Select EventID` をクエリの末尾に追加します。
- 最後に、`| Sort EventID asc` をクエリの末尾に追加します。


注意する必要がある重要なポイントがいくつかあります。

1 つ目のポイントは、表示される結果は、元の生の結果ではなくなっていることです。それらは集計された結果であり、本質的に結果のグループです。これは問題ではありませんが、集計/統計関数の適用によって生成された元の生のデータの形とは非常に異なっていることを理解しておく必要があります。

2 つ目のポイントは、**Measure count** は、現時点では上位 100 件の独自の結果のみを返すことです。この制限は、その他の統計関数には適用されません。このため、Measure count() は、通常は詳細なフィルターを使用して特定の項目を検索した後で適用する必要があります。

## Measure コマンドで Max 関数と Min 関数を使用する

**Measure Max()** と **Measure Min()** は、さまざまな状況で役に立ちます。ただし、これらの関数は逆の動作をするため、ここでは Max() について説明します。Min() については自分で試すことができます。

Configuration Assessment アラートをクエリする場合、それらのアラートには、情報、警告、および重大を表す値 (0、1、および 2) が格納される **Severity** プロパティがあります。次に例を示します。

```
Type=ConfigurationAlert
```

![Measure count() を使用した検索の開始](./media/operational-insights-search/search-measure-max01.png)

コンピューターのすべてのアラートの最大値を確認する場合は、次のクエリを使用してフィールド別にグループ化できます。

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![Measure Max を使用したコンピューターの検索](./media/operational-insights-search/search-measure-max02.png)

**Alert** レコードがあるコンピュータの大部分で少なくとも 1 件の重大なアラートが発生し、Bacc コンピューターでは最悪のアラートとして 1 件の警告が発生していることが表示されます。

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![Measure Max(TimeGenerated) を使用したコンピューターの検索](./media/operational-insights-search/search-measure-max03.png)

この関数は数値に適していますが、DateTime フィールドでも動作します。各コンピューターでインデックス作成されたデータの最後または最近のタイムスタンプをチェックするために役立ちます。例: 各コンピューターの変更追跡ソリューションによって報告された最新の構成変更はいつ発生したか。

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## Measure コマンドで Avg 関数を使用する

Measure で使用される Avg() 統計関数では、あるフィールドの平均値を計算でき、結果を同じフィールドまたは別のフィールド別にグループ化できます。これは、パフォーマンス データなどのさまざまなケースで役立ちます。

パフォーマンス データから始めます。ただし、Operational Insights は、現時点では容量管理ソリューションの一部として Virtual Machine Manager と Hyper-V ホストの特定のファブリック関連のパフォーマンス カウンターのみ収集することに注意してください。

*すべての*パフォーマンス データを検索するための最も基本的なクエリは次のとおりです。

```
Type=PerfHourly
```

![Avg を使用した検索の開始](./media/operational-insights-search/search-avg01.png)

最初に気付くことは、Operational Insights はパフォーマンス カウンターのグラフを表示することです。結果の下部に、グラフの背後にある実際のレコードが表示されます。

![Avg を使用した検索の開始](./media/operational-insights-search/search-avg02.png)

上記の図には、以下を示す 2 つのフィールド セットがマークされています。

- 最初のセットは、クエリ フィルター内の Windows パフォーマンス カウンターの名前、オブジェクト名、およびインスタンス名を識別します。これらは、ファセット/フィルターとして最もよく使用される可能性があるフィールドです。
- **SampleValue** は、カウンターの実際の値です。
- クエリの **Type=PerfHourly** は、1 時間ごとの集計です。
- **TimeGenerated** は 21 時 00分であり、これは 24 時間制の時刻です。20 時 00分から 21 時 00分までの 1 時間のデータが集計されます。
- **SampleCount** は、12 のサンプル (5 分ごとに 1 つ) を使用して計算された集計です。
- 時間単位の期間の **min**、**max**、および **Percentile95** は、この例では仮想マシンのメモリのものであり、6144 (メガバイト単位) です。
- **SampleValue** は、1 時間ごとの集計であり、時間単位の期間の*平均*を示します。これがパフォーマンス グラフを描画するために使用される値です。

PerfHourly レコードの形と他の検索テクニックに関する情報を読み取った後、Measure Avg() を使用して、この種の数値データを集計できます。

単純な例を次に示します。

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(SampleValue) by Computer
```

![Avg(SampleValue) を使用した検索](./media/operational-insights-search/search-avg03.png)

この例では、CPU の合計時間に関するパフォーマンス カウンターを選択し、コンピューター別に平均を計算します。**SampleValue** は既に平均値であるため、実際は、平均の平均をクエリします。この時点の Type=PerfHourly では合っています。TimeGenerated に常にフィルターを適用して、操作の対象を小さなまたは最近のデータセットに制限する必要があります (たとえば、過去 7 日間ではなく 4 時間にします)。

したがって、上記のクエリは次のようになります。

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer
```

### Measure コマンドで Avg 関数を使用して検索するには
1. 検索クエリ ボックスに、「`Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer`」と入力します。
2. 最近の平均値は通常は高くなることに注意してください。
3. 検索クエリを `Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(Max) by Computer` に変更して、1 時間ごとの最大値の平均を計算します。

*複数の*コンピューターのデータを集計して相関させることができます。たとえば、ある種のファームの中にホストのセットがあり、各ノードは他のノードと同等であり、同じ種類の作業を実行し、負荷はほぼ均等であるとします。次のクエリを実行することで、すべてのカウンターを一気に取得し、ファーム全体の平均値を取得できます。次の例のように、コンピューターを選択することから始めます。

```
Type=PerfHourly AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

コンピューターを選択したので、次に 2 つの主要業績評価指標 (KPI) である CPU 使用率と空きディスク領域の割合のみを選択します。したがって、クエリのその部分は次のようになります。

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

これで、次の例のように、コンピューターとカウンターを追加できます。

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

非常に具体的な選択を行っているため、**Measure Avg()** コマンドは、コンピューター別ではなく、ファーム全体の平均を、CounterName でグループ化して返すことができます。次に例を示します。

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”) | Measure Avg(SampleValue) by CounterName
```

これにより、環境のいくつかの KPI に関するコンパクトで有用なビューを取得できます。

![Avg を使用した検索のグループ化](./media/operational-insights-search/search-avg04.png)


これは、ダッシュボードで簡単に使用できます。ダッシュボードの使用の詳細については、「[Operational Insights ダッシュボード](operational-insights-use-dashboards)」を参照してください。

![Avg を使用した検索結果を示すダッシュボード](./media/operational-insights-search/search-avg05.png)

### Measure コマンドで Sum 関数を使用する

Sum 関数は、Measure コマンドの他の関数に似ています。Sum 関数の使用方法の例については、「[Microsoft Azure Operational Insights での W3C IIS ログ検索](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx)」で確認できます。

Max() と Min() は、数値、日付時刻、およびテキスト文字列で使用できます。テキスト文字列はアルファベット順に並べ替えられ、最初と最後の文字列が取得されます。

ただし、Sum() は、数値フィールド以外では使用できません。これは Avg() にも当てはまります。

## Where コマンドを使用する

Where コマンドはフィルターのように機能しますが、パイプライン内で適用して、クエリの先頭でフィルター処理される生データではなく、Measure コマンドによって生成された集計結果をさらにフィルター処理できます。

次に例を示します。

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer
```

次の例に示すように、別のパイプ文字 "|" と Where コマンドを追加することで、平均 CPU 使用率が 80% を超えるコンピューターのみを取得できます。

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer | Where AVGCPU>80
```

Microsoft System Center の Operations Manager に慣れている場合、Where コマンドは管理パック用語と考えることができます。上記の例をルールであるとみなすと、クエリの最初の部分はデータ ソースになり、Where コマンドは条件の検出にあたります。

クエリは、**[マイ ダッシュボード]** のタイルとして、コンピューターの CPU が過剰に使用されたときに確認するためのある種のモニターとして使用できます。ダッシュボードの詳細については、「[Operational Insights ダッシュボード](operational-insights-use-dashboards)」を参照してください。ダッシュボードは、モバイル アプリで作成して使用することもできます。詳細については、「[Azure Operational Insights モバイル アプリ](http://www.windowsphone.com/ja-jp/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)」を参照してください。次の図の下部にある 2 つのタイルに、モニターが一覧と数値で表示されていることがわかります。基本的には、数値はゼロであり、一覧は空であることが望まれます。そうでない場合は、アラート状態であることを示しています。必要に応じて、どのコンピューターに負荷がかかっているかを調べることができます。

![モバイル ダッシュボード](./media/operational-insights-search/search-mobile.png)

## 検索構文リファレンス

検索言語に関する次の参照セクションは、データを検索するときに使用できる一般的なクエリ構文のオプションと、検索を絞り込むために使用できるフィルター式について説明しています。取得されたデータに対して何らかのアクションを実行するために使用できるコマンドについても説明しています。

検索で返されるフィールドと、データの類似するカテゴリにドリルダウンするために役立つファセットについては、「[検索フィールドとファセットのリファレンス](#Search-field-and-facet-reference)」を参照できます。

### 一般的なクエリ構文

構文:

filterExpression | command1 | command2 …

フィルター式 (\*\*filterExpression\*\*) は、クエリの "where" 条件を定義します。コマンドは、クエリによって返された結果に適用されます。複数のコマンドは、パイプ文字 (|) で区切る必要があります。

#### 一般的な構文の例

次に例を示します。

	system

このクエリは、フル テキストまたは用語検索用のインデックスが設定されている任意のフィールドに "system" という単語を含む結果を返します。

>[AZURE.NOTE]すべてのフィールドがこのようにインデックス設定されているわけではありませんが、最も一般的なテキスト フィールド (説明や名前など) には通常は設定されています。

	system error

このクエリは、"system" と "error" という単語を含む結果を返します。

	system error | sort ManagementGroupName, TimeGenerated desc | top 10

このクエリは、"system" と "error" という単語を含む結果を返します。 結果は、**ManagementGroupName** フィールドによって (昇順に) 並べ替えられた後、**TimeGenerated** によって (降順に) 並べ替えられます。最初の 10 個の結果だけが取得されます。

>[AZURE.IMPORTANT]すべてのフィールド名と、文字列フィールドとテキスト フィールドの値では、大文と字小文字が区別されます。

### フィルター式

以降のサブセクションでは、フィルター式について説明します。

#### 文字列リテラル

文字列リテラルは、パーサーによってキーワードまたは定義済みのデータ型 (数値や日付など) として認識されないすべての文字列です。

次に例を示します。

	These all are string literals


このクエリは、5 つの単語のすべてが出現する結果を検索します。複雑な文字列検索を実行するには、リテラル文字列を引用符で囲みます。次に例を示します。

	" Windows Server"

これは、"Windows Server" と完全に一致する結果のみを返します。

#### 数値

パーサーは、数値フィールドで 10 進数の整数と浮動小数点数をサポートします。

次に例を示します。

	Type:PerfHourly 0.5


	HTTP 500

#### 日付/時刻

システム内のすべてのデータは **TimeGenerated** プロパティを持っています。これはレコードの元の日付と時刻を表します。データの種類によっては、さらに多くの日付/時刻フィールドを持つことができます (たとえば **LastModified**)。

Operational Insights のタイムライン グラフ/タイムセレクターは、**TimeGenerated** フィールドに基づいて、(実行されている現在のクエリに従って) 時間の経過による結果の分布を示します。日付/時刻フィールドには、クエリを特定の期間に制限するためにクエリ内で使用できる文字列の形式があります。相対的な期間 (たとえば "3 日前と 2 時間前の間") を参照する構文を使用することもできます。

構文:

	yyyy-mm-ddThh:mm:ss.dddZ


	yyyy-mm-ddThh:mm:ss.ddd


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm


	yyyy-mm-dd



例:

	TimeGenerated:2013-10-01T12:20

上のコマンドは、**TimeGenerated** の値が 2013 年 10 月 1 日 12 時 20 分であるレコードのみを返します。このコマンドで結果が返る可能性はほとんどないのですが、考え方を理解してください。

パーサーは、ニーモニックな日付/時刻値である NOW もサポートします。

例:


ここでも、データがシステムをそれほど高速で通過することはないため、結果を得られる可能性はほとんどありません。

これらの例は、相対的な日付と絶対的な日付を使用するための構成要素です。次の 3 つのサブセクションでは、高度なフィルター内でのそれらの使用方法について、相対的な日付範囲を使用する例を用いて説明します。

#### 日付/時刻の計算

日付/時刻算術演算子を使用して、単純な日付/時刻計算による日付/時刻値をオフセットするか丸めます。

構文:

	datetime/unit


	datetime[+|-]count unit




<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th>演算子</th>
		<th>説明</th>
	</tr>
	<tr>
		<td>
		<p>/</p>
		</td>
		<td>
		<p>日付/時刻を指定された単位に丸めます。</p>
		<p>例: NOW/DAY は、現在の日付/時刻を、当日の午前 0 時に丸めます。</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>+ または -</p>
		</td>
		<td>
		<p>日付/時刻を、指定された単位数でオフセットします。</p>
		<p>次に例を示します。&#160; </p>
		<ul>
			<li class="unordered">NOW+1HOUR は、現在の日付/時刻を、1 時間先にオフセットします。<br><br></li>
			<li class="unordered">2013-10-01T12:00-10DAYS は、日付値を 10 日間過去にオフセットします。</li>
		</ul>
		</td>
	</tr>
</table>





日付/時刻算術演算子は連結できます。次に例を示します。

	NOW+1HOUR-10MONTHS/MINUTE

次の表は、サポートされる日付/時刻の単位を一覧表示しています。

日付と時刻の単位|説明
---|--- 
YEAR、YEARS|現在の年に丸めるか、指定された年数分オフセットします。
MONTH、MONTHS|現在の月に丸めるか、指定された月数分オフセットします。
DAY、DAYS、DATE|現在の日に丸めるか、指定された日数分オフセットします。
HOUR、HOURS|現在の時に丸めるか、指定された時数分オフセットします。
MINUTE、MINUTES|現在の分に丸めるか、指定された分数分オフセットします。
SECOND、SECONDS|現在の秒に丸めるか、指定された秒数分オフセットします。
MILLISECOND、MILLISECONDS、MILLI、MILLIS|現在のミリ秒に丸めるか、指定されたミリ秒数分オフセットします。


#### フィールドのファセット

フィールドのファセットを使用することで、インデックスのさまざまな用語に対する "フリー テキスト" のクエリを記述するのではなく、特定のフィールドに対する検索条件と正確な値を指定できます。この構文は、これまでの段落のさまざまな例の中で既に使用しています。ここでは、さらに複雑な例を示します。

**構文**

*field:value*

*field=value*

**説明**

フィールドで特定の値を検索します。値は、文字列リテラル、数値、または日付/時刻を指定できます。

例:


	TimeGenerated:NOW


	ObjectDisplayName:"server01.contoso.com"


	SampleValue:0.3

**構文**

*field>value*

*field<value*

*field>=value*

*field<=value*

*field!=value*

**説明**

比較を指定します。

例:

	TimeGenerated>NOW+2HOURS


**構文**

*field:[from..to]*

**説明**

範囲ファセットを指定します。

例:

	TimeGenerated:[NOW..NOW+1DAY]


	SampleValue:[0..2]
#### 論理演算子

クエリ言語は、論理演算子 (AND、OR、および NOT) と、C スタイルの別名 (&&、||、および !) をサポートします。かっこを使用して、これらの演算子をグループ化できます。

次に例を示します。

	system OR error


	Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
最上位レベルのフィルター引数の論理演算子は省略できます。この場合は、AND 演算子とみなされます。


フィルター式|相当する構文
---|---
system error|system AND error
system "Windows Server" OR Severity:1|system AND ("Windows Server" OR Severity:1)



### コマンド

コマンドは、クエリによって返された結果に適用されます。取得された結果にコマンドを適用するには、パイプ文字 (|) を使用します。複数のコマンドは、パイプ文字で区切る必要があります。

>[AZURE.NOTE]コマンド名は、フィールド名とデータとは異なり、大文字でも小文字でも記述できます。

#### 並べ替え

構文:

	sort field1 asc|desc, field2 asc|desc, …

結果を特定のフィールドを基準に並べ替えます。asc/desc プレフィックスは省略可能です。これらを省略した場合、並べ替え順序は "asc (故昇順)" であるとみなされます。クエリで **Sort** コマンドが明示的に使用されない場合、既定の動作である Sort **TimeGenerated** desc によって常に最新の結果が最初に戻ります。

#### Top/Limit

構文:

	top number


	limit number
応答を上位 N 個の結果に制限します。

例:

	Type:Alert errors detected | top 10

上位 10 個の一致する結果を返します。

#### Skip

構文:

	skip number

表示される結果の数をスキップします。

例:

	Type:Alert errors detected | top 10 | skip 200

結果 200 から始まる上位 10 個の一致する結果を返します。

#### [

構文:

	select field1, field2, ...

結果を選択したフィールドに制限します。

例:

	Type:Alert errors detected | select Name, Severity

返される結果のフィールドを **Name** と **Severity** に制限します。

#### Measure

**Measure** コマンドは、生の検索結果に統計関数を適用するために使用されます。これは、データの *グループ別*の表示を取得するときに非常に役立ちます。**Measure** コマンドを使用すると、Operational Insights では、集計結果が表で示されます。

構文:

	measure aggregateFunction([aggregatedField]) [as fieldAlias] by groupField [interval interval]


	measure aggregateFunction([aggregatedField])  interval interval

結果を **groupField** 別に集計し、集計されたメジャー値を **aggregatedField** を使用して計算します。


<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>Measure 統計関数</th> <th>説明</th> </tr> <tr> <td> <p><em>aggregateFunction</em> </p> <p></p> </td> <td> <p>集計関数の名前 (大文字と小文字の区別あり)。次の集計関数がサポートされます。</p> <ul> <li class="unordered">COUNT<br><br></li> <li class="unordered">MAX<br><br></li> <li class="unordered">MIN<br><br></li> <li class="unordered">SUM<br><br></li> <li class="unordered">AVG<br><br></li> <li class="unordered">STDDEV<br><br></li> </ul> </td> </tr> <tr> <td> <p><em>aggregatedField</em> </p> </td> <td> <p>集計されるフィールド。このフィールドは COUNT 集計関数では省略可能ですが、SUM、MAX、MIN、AVG、または STDDEV では既存の数値フィールドである必要があります。</p> </td> </tr> <tr> <td> <p><em>fieldAlias</em> </p> </td> <td> <p>計算される集計値の別名 (省略可能)。指定がない場合、フィールド名は <em>AggregatedValue になります。</em></p> </td> </tr> <tr> <td> <p><em>groupField</em> </p> </td> <td> <p>結果セットをグループ化するために使用されるフィールドの名前。</p> </td> </tr> <tr> <td> <p><em>Interval</em> </p> </td> <td> <p>次の書式の期間。</p> <p><em>nnnNAME</em> </p> <p></p> <p>ここで: </p> <p>nnn は正の整数</p> <p><em>NAME</em> は期間名</p> <p>サポートされる期間名 (大文字と小文字の区別あり): </p> <ul> <li class="unordered">MILLISECOND[S]<br><br></li> <li class="unordered">SECOND[S]<br><br></li> <li class="unordered">MINUTE[S]<br><br></li> <li class="unordered">HOUR[S]<br><br></li> <li class="unordered">DAY[S]<br><br></li> <li class="unordered">MONTH[S]<br><br></li> <li class="unordered">YEAR[S]<br></li> </ul> </td> </tr> </table>



期間オプションは、日付/時刻グループ フィールド (**TimeGenerated** や **TimeCreated** など) でのみ使用できます。現時点では、これはサービスでは強制されていませんが、日付/時刻のないフィールドがバックエンドに渡されるとランタイム エラーが発生します。スキーマの検証が実装されるときに、サービス API は、日付/時刻のないフィールドを使用して期間集計を行うクエリを拒否します。現時点での **Measure** の実装は、**Count** 集計関数の期間のグループ化のみサポートします。

BY 句は省略されたが (2 番目の構文として) 期間が指定された場合は、既定で **TimeGenerated** フィールドとみなされます。

次に例を示します。

**例 1**

	Type:Alert | measure count() as Count by ObjectId

*説明*

アラートを **ObjectID** 別にグループ化し、各グループのアラート数を計算します。集計値が **Count** フィールド (別名) として返されます。

**例 2**

	Type:Alert | measure count() interval 1HOUR

*説明*

アラートを **TimeGenerated** フィールドを使用して 1 時間の期間でグループ化し、各期間のアラート数を返します。

**例 3**

	Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*説明*

前の例と同じですが、 集計フィールドの別名 (\*\*AlertsPerHour\*\*) が使用されています。

**例 4**

	* | measure count() by TimeCreated interval 5DAYS

*説明*

結果を **TimeGenerated** フィールドを使用して 5 日間の期間でグループ化し、各期間の結果数を返します。

**例 5**

	Type:Alert | measure max(Severity) by WorkflowName

*説明*

アラートをワークロードの名前でグループ化し、各ワークフローでのアラートの重大度の最大値を返します。

**例 6**

	Type:Alert | measure min(Severity) by WorkflowName

*説明*

前の例と同じですが、**Min** 集計関数が使用されています。

**例 7**

	Type:PerfHourly | measure avg(SampleValue) by ObjectId

*説明*

PerfHourly を ObjectId によってグループ化し、平均 (avg) を計算します。

**例 8**

	Type:PerfHourly | measure sum(SampleValue) by ObjectId

*説明*

前の例と同じですが、**Sum** が使用されています。

**例 9**

	Type:PerfHourly | measure stddev(SampleValue) by ObjectId

*説明*

前の例と同じですが、**STDDEV** が使用されています。

**例 10**

	Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*説明*

アラートの数が最大である上位 5 つのワークフローを取得します。

#### Where

構文:

**where** AggregatedValue>20

**Measure** 集計関数によって生成された集計結果をさらにフィルター処理するために **Measure** コマンドの後でのみ使用できます。

次に例を示します。

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) as MAXCPU by

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) by RootObjectName | where (AggregatedValue>50 and AggregatedValue<90)




## 検索フィールドとファセットのリファレンス

検索を使用してデータを検索すると、結果にさまざまなフィールドとファセットが表示されます。ただし、表示される一部の情報がわかりにくい場合があります。次の情報は、結果を理解するために役立ちます。

<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th><b>フィールド</b></th>
		<th><b>検索の種類<b></th>
		<th><b>説明</b></th>
	</tr>
	<tr>
		<td>
		<p>TenantId</p>
		</td>
		<td>
		<p>すべて</p>
		</td>
		<td>
		<p>データをパーティション分割するために使用されます</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeGenerated</p>
		</td>
		<td>
		<p>すべて</p>
		</td>
		<td>
		<p>(検索と他の画面で) タイムライン、タイムセレクターを動作させるために使用されます。これは (通常はエージェント上で) データが生成された時間を表します。時間は ISO 形式で表され、常に UTC です。既存のインストルメンテーションに基づく '種類' (つまりログ内のイベント) の場合、これは通常はログ エントリ/行/レコードが記録された実際の時間。管理パックまたはクラウド経由で生成されたその他の種類 (つまり、推奨事項/アラート/updateagent/その他) の場合は、何らかの構成のスナップショットを使用して新しいデータが収集された時間、またはそれに基づいて推奨事項/アラートが生成された時間。</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventID</p>
		</td>
		<td>
		<p>イベント</p>
		</td>
		<td>
		<p>Windows イベント ログ内のイベント ID</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLog</p>
		</td>
		<td>
		<p>イベント</p>
		</td>
		<td>
		<p>Windows によってイベントが記録されたイベント ログ</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevelName</p>
		</td>
		<td>
		<p>イベント</p>
		</td>
		<td>
		<p>重大/警告/情報/成功</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevel</p>
		</td>
		<td>
		<p>イベント</p>
		</td>
		<td>
		<p>重大/警告/情報/成功を表す数値 (より簡単で読みやすいクエリにするには EventLevelName を代りに使用します)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceSystem</p>
		</td>
		<td>
		<p>すべて</p>
		</td>
		<td>
		<p>データ ソース (サービスへの '接続' モードの観点から。サービスとは Operations Manager、Operational Insights (データはクラウド内に生成されます)、Azure Storage (データ ソースは WAD) などです。</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Windows パフォーマンス オブジェクト名</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>InstanceName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Windows パフォーマンス カウンターのインスタンス名</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>CounteName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Windows パフォーマンス カウンターの名前</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectDisplayName</p>
		</td>
		<td>
		<p>PerfHourly、ConfigurationAlert、ConfigurationObject、ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Operations Manager でパフォーマンス収集ルールの対象となったオブジェクトの表示名、または Operational Insights によって検出またはアラートが生成されたオブジェクトの表示名。</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RootObjectName</p>
		</td>
		<td>
		<p>PerfHourly、ConfigurationAlert、ConfigurationObject、ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Operations Manager でパフォーマンス収集ルールの対象となったオブジェクトの親の親の表示名 (二重ホスティング関係。つまり、Windows コンピューターにホストされる SqlInstance によってホストされる SqlDatabase)、または Operational Insights によって検出またはアラートが生成されたオブジェクトの表示名。</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>コンピューター</p>
		</td>
		<td>
		<p>ほとんどの種類 </p>
		</td>
		<td>
		<p>データが属しているコンピューターの名前</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DeviceName</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>データが属しているコンピューターの名前 ('Computer' と同じ)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DetectionId</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatusRank</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>脅威の状態のランクは脅威の状態の数値表現であり、HTTP 応答コードに似ています。数値の飛びは新しい状態を追加できる余地を残すためです (脅威なしを示す数値が 100 でも 0 でもなく 150 なのはこれが理由です)。驚異の状態と保護の状態のロールアップでは、選択された期間内のコンピューターの状態で、最悪のものが表示されます。数値を使用して異なる状態にランクを付けて、数値が最も高いレコードを検索できるようになっています。</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatus</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>ThreatStatus の説明。ThreatStatusRank と 1 対 1 で対応しています。</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TypeofProtection</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>コンピューターで検出されたマルウェア対策製品。なし、Microsoft Malware Removal Tool、Forefront など</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ScanDate</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceHealthServiceId</p>
		</td>
		<td>
		<p>ProtectionStatus、RequiredUpdate</p>
		</td>
		<td>
		<p>このコンピューターのエージェント用の HealthService ID</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>HealthServiceId</p>
		</td>
		<td>
		<p>ほとんどの種類 </p>
		</td>
		<td>
		<p>このコンピューターのエージェント用の HealthService ID</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ManagementGroupName</p>
		</td>
		<td>
		<p>ほとんどの種類 </p>
		</td>
		<td>
		<p>Operations Manager に接続されたエージェントの管理グループの名前。それ以外の場合は null/空白</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectType</p>
		</td>
		<td>
		<p>ConfigurationObject</p>
		</td>
		<td>
		<p>Operational Insights の構成評価で検出されたこのオブジェクトの種類 (Operations Manager Management Pack の '型'/クラスに類似)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateTitle</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>インストールされていないことが検出された更新プログラムの名前</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>PublishDate</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>更新プログラムが Microsoft Update に公開された日時</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>サーバー</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>データが属しているコンピューターの名前 ('Computer' と同じ)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>製品</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>更新プログラムが適用される製品</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateClassification</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>更新の種類 (更新プログラムのロールアップ、Service Pack など)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBID</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>このベスト プラクティスまたは更新プログラムについて説明しているサポート技術情報の ID</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WorkflowName</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>アラートを生成したルールまたはモニターの名前</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>重大度</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>アラートの重大度</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>優先順位</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>アラートの優先度</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>IsMonitorAlert</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>このアラートがモニターとルールのどちらによって生成されたか (モニターの場合は true、ルールの場合は fase)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AlertParameters</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Operational Insights アラートのパラメーターを使用する XML</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Context</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Operational Insights アラートの 'コンテキスト' を使用する XML</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ワークロード</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>アラートが参照するテクノロジまたは 'ワークロード' </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AdvisorWorkload</p>
		</td>
		<td>
		<p>推奨</p>
		</td>
		<td>
		<p>推奨事項が参照するテクノロジまたは 'ワークロード'</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>説明</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>アラートの説明 (簡略版)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdate</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>(このレコードの 'TimeGenerated' の日付から) 何日前にこのエージェントが WSUS/Microsoft Update から更新プログラムをインストールしたかを示す日数</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdateBucket</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>WSUS/Microsoft Update の更新プログラムが何日前にコンピューターにインストールされたかを示す、DaysSinceLastUpdate に基づく 'タイム バケット' 分類</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateEnabled</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>このエージェントでの更新プログラムの自動チェックの設定 (有効/無効)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateValue</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>更新プログラムの自動チェックの設定内容 (自動的にダウンロードしてインストール、ダウンロードのみ、またはチェックのみ)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WindowsUpdateAgentVersion</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Microsoft Update エージェントのバージョン番号</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WSUSServer</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>この更新エージェントのターゲット WSUS サーバー</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>OSVersion</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>この更新エージェントが実行されているオペレーティング システムのバージョン</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>名前</p>
		</td>
		<td>
		<p>Recommendation、ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>推奨事項の名前/タイトル、または Operational Insights の構成評価でのプロパティの名前</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>値</p>
		</td>
		<td>
		<p>ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Operational Insights の構成評価でのプロパティの値</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBLink</p>
		</td>
		<td>
		<p>推奨</p>
		</td>
		<td>
		<p>このベスト プラクティスまたは更新プログラムについて説明しているサポート技術情報の URL</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RecommendationStatus</p>
		</td>
		<td>
		<p>推奨</p>
		</td>
		<td>
		<p>推奨事項は、単に検索インデックスに追加されるのではなく、そのレコードが '更新される' 数少ない種類のデータです。この状態は、推奨事項の状態 (アクティブ/オープン) や、それが解決されているかどうかのOperational Insights による検出の有無によって変化します。</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RenderedDescription</p>
		</td>
		<td>
		<p>イベント</p>
		</td>
		<td>
		<p>Windows イベントの表示の説明 (設定されたパラメーターを含む再使用テキスト)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ParameterXml</p>
		</td>
		<td>
		<p>イベント</p>
		</td>
		<td>
		<p>Windows イベントの 'データ' セクションのパラメーターを使用する XML (イベント ビューアーに表示)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventData</p>
		</td>
		<td>
		<p>イベント</p>
		</td>
		<td>
		<p>Windows イベントの 'データ' セクション全体のパラメーターを使用する XML (イベント ビューアーに表示)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>から</p>
		</td>
		<td>
		<p>イベント</p>
		</td>
		<td>
		<p>イベントを生成したイベント ログのソース</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventCategory</p>
		</td>
		<td>
		<p>イベント</p>
		</td>
		<td>
		<p>Windows イベント ログから直接取得されるイベントのカテゴリ</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UserName</p>
		</td>
		<td>
		<p>イベント</p>
		</td>
		<td>
		<p>Windows イベントのユーザー名 (通常は NT AUTHORITY\LOCALSYSTEM)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleValue</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>パフォーマンス カウンターの 1 時間ごとの集計の平均値 </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Min</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>パフォーマンス カウンターの 1 時間ごとの集計の最小値</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Max</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>パフォーマンス カウンターの 1 時間ごとの集計の最大値</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Percentile95</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>パフォーマンス カウンターの 1 時間ごとの集計の 95 パーセンタイル値</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleCount</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>この 1 時間ごとの集計レコードを生成するために使用された '生' のパフォーマンス カウンターのサンプルの数</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Threat</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>検出されたマルウェアの名前</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>StorageAccount</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>ログの読み取りが行われた Azure ストレージ アカウント</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AzureDeploymentID</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>ログが属しているクラウド サービスの Azure デプロイ ID</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ロール</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>ログが属している Azure クラウド サービスのロール</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RoleInstance</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>ログが属している Azure ロールの RoleInstance</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sSiteName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>ログが属している IIS Web サイト (メタベース表記); 元のログの s-sitename フィールド</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sComputerName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>元のログの s computername フィールド</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>HTTP 要求が送信されたサーバーの IP アドレス。元のログの s-ip フィールド</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csMethod</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>HTTP 要求でクライアントによって使用される HTTP メソッド (GET/POST/その他)。元のログの cs-method</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>cIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>HTTP 要求の送信元クライアントの IP アドレス。元のログの c-ip フィールド</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserAgent</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>クライアントによって宣言された HTTP ユーザー エージェント (ブラウザーまたはそれ以外)。元のログの cs-user-agent </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>サーバーによってクライアントに返された HTTP 状態コード (200/403/500/その他)。元のログの cs-status</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeTaken</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>要求が完了するまでにかかった時間 (ミリ秒単位)。元のログの timetaken フィールド</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriStem</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>要求された相対 URI (ホスト アドレスなし。つまり '/search') 。元のログの cs-uristem フィールド</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriQuery</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>URI クエリ。URI クエリは ASP ページなどの動的なページに対してのみ必要であるため、このフィールドには通常は静的なページ用のハイフンが含まれます。</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sPort</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>HTTP 要求が送信された (およびIIS がピックアップしてリッスンする) サーバーのポート</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>認証されたユーザーの名前 (要求が認証され、匿名でない場合)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csVersion</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>要求で使用された HTTP プロトコルのバージョン (つまり 'HTTP/1.1')</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csCookie</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Cookie 情報</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csReferer</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>ユーザーが最後にアクセスしたサイト。現在のサイトへのリンクを提供したサイト</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csHost</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>要求されたホスト ヘッダー (つまり 'www.mysite.com')</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scSubStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Substatus エラー コード</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scWin32Status</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Windows 状態コード</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>クライアントからサーバーへの要求で送信されたバイト数</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>サーバーからクライアントへの応答で返されたバイト数</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ConfigChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>変更の種類 (WindowsServices/ソフトウェア/その他)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ChangeCategory </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>変更のカテゴリ (変更/追加/削除)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>ソフトウェアの種類 (更新プログラム/アプリケーション)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>ソフトウェアの名前 (ソフトウェアの変更にのみ適用)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>発行元 </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>ソフトウェアを公開しているベンダー (ソフトウェアの変更にのみ適用)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Windows サービスに適用された変更の種類 (State / StartupType / Path / ServiceAccount)。Windows サービスの変更にのみ適用</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDisplayName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>変更されたサービスの表示名</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>変更されたサービスの名前</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcState </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>サービスの新しい (現在の) 状態</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousState</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>サービスの直前の既知の状態 (サービスの状態が変更された場合のみ適用)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcStartupType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>サービスのスタートアップの種類</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousStartupType</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>直前のサービスのスタートアップの種類 (サービスのスタートアップの種類が変更された場合のみ適用)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcAccount </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>サービス アカウント</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousAccount</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>直前のサービス アカウント (サービス アカウントが変更された場合のみ適用)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Windows サービスの実行可能ファイルへのパス</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Windows サービスの実行可能ファイルの直前のパス (パスが変更された場合にのみ適用)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDescription</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>サービスの説明</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>前へ </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>このソフトウェアの直前の状態 (インストールされている/インストールされていない/直前のバージョン)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Current</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>このソフトウェアの最新の状態 (インストールされている/インストールされていない/現在のバージョン)</p>
		</td>
	</tr>
</table>

## ブログ記事 - 検索機能の使用例
- [Microsoft Azure オペレーション インサイトでの W3C IIS ログ検索](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx)
- [Azure オペレーション インサイトの検索とダッシュボードで SQL バックアップ エラーを監視する](http://blogs.msdn.com/b/dmuscett/archive/2015/02/21/monitoring-sql-backup-failures-with-azure-operational-insights-search-and-dashboards.aspx)
- [IIS 管理パックのイベント アラート ルールでオペレーション インサイトの検索と同等の処理を行う](http://blogs.msdn.com/b/dmuscett/archive/2014/11/05/iis-mp-event-alerting-rules-s-opinsights-searches-equivalents.aspx)
- [オペレーション インサイトの便利な検索クエリ集](http://blogs.msdn.com/b/dmuscett/archive/2014/10/19/advisor-searches-collection.aspx)

## その他のリソース
Stefan Roth が、使いやすい検索チート シートを作成しています。詳しくは彼の[ブログ](http://stefanroth.net/2014/11/05/microsoft-azure-operational-insights-search-data-explorer-cheat-sheet/)を参照してください。チート シートをダウンロードできます。

<!---HONumber=July15_HO5-->