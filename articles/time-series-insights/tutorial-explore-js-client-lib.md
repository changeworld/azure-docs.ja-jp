---
title: Time Series Insights JavaScript クライアント ライブラリを調べる
description: Time Series Insights JavaScript クライアント ライブラリと、関連するプログラミング モデルについて説明します。
documentationcenter: ''
services: time-series-insights
author: BryanLa
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: time-series-insights
ms.workload: na
ms.tgt_pltfrm: ''
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2018
ms.author: bryanla
ms.openlocfilehash: 5b845f36dbb65b38d0e4ac2a118277027239b3d6
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
---
# <a name="tutorial-explore-the-time-series-insights-javascript-client-library"></a>チュートリアル: Time Series Insights JavaScript クライアント ライブラリを調べる

Time Series Insights (TSI) に格納されたデータを照会して視覚化する開発者を支援するために、この作業を容易にする JavaScript D3 ベースのコントロール ライブラリを開発しました。 このチュートリアルでは、サンプルの Web アプリケーションを使用して、TSI JavaScript クライアント ライブラリと、関連するプログラミング モデルについて説明します。 

説明されている各トピックで、実習、TSI データへのアクセス方法の深い理解、およびグラフ コントロールを使用したデータのレンダリングと視覚化の機会が得られます。 目的は、独自の Web アプリケーションでライブラリを使用するために必要な詳しい情報を提供することです。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * TSI サンプル アプリケーション 
> * TSI JavaScript クライアント ライブラリ
> * サンプル アプリケーションでライブラリを使用して TSI データを視覚化する方法

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、[Edge](/microsoft-edge/devtools-guide)、[Chrome](https://developers.google.com/web/tools/chrome-devtools/)、[FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools)、[Safari](https://developer.apple.com/safari/tools/) など、最新の Web ブラウザーにある "開発者ツール" 機能 (DevTools、F12 とも呼ばれます) を使用します。 まだ使い慣れていない場合は、先に進む前に、自分のブラウザーの機能を調べてみることをお勧めします。 

## <a name="the-time-series-insights-sample-application"></a>Time Series Insights のサンプル アプリケーション

このチュートリアルでは、Time Series Insights サンプル アプリケーションを使用して、TSI JavaScript クライアント ライブラリの使用法など、アプリケーションの背後にあるソース コードを調べます。 このアプリケーションは単一ページ Web アプリケーション (SPA) であり、サンプル TSI 環境のデータを照会および視覚化するためにライブラリを使用する方法を示しています。 

1. [Time Series Insights のサンプル アプリケーション](https://insights.timeseries.azure.com/clientsample)に移動します。 次のような、サインインを求めるページが表示されます。![TSI Client Sample のサインインを求めるメッセージ](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. [ログイン] ボタンをクリックし、資格情報を入力するか選択します。 エンタープライズ/組織アカウント (Azure Active Directory) または個人アカウント (Microsoft アカウント、または MSA) を使用することができます。 

   ![TSI Client Sample の資格情報プロンプト](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. サインインに成功すると、次のようなページが表示されます。これらのページには、TSI データが読み込まれたいくつかのスタイルのサンプル グラフが表示されます。 また、右上のユーザー アカウントと "ログアウト" リンクに注意してください。![サインイン後の TSI Client Sample のメイン ページ](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>ページのソースと構造

まず、ブラウザーでレンダリングされたページの基になる HTML と JavaScript のソース コードを見てみましょう。 すべての要素を見ていくわけではありませんが、主要なセクションについては説明するので、ページがどのように動作するかを把握することができます。

1. ブラウザーで "開発者ツール" を開き、現在のページを構成する HTML 要素 (HTML または DOM ツリーとも呼ばれます) を調べます。

2. `<head>` と `<body>` 要素を展開し、次のセクションに注目してください。
   - `<head>` の下には、ページの機能を補助するために追加のファイルを取り込む要素があります。
     - Azure Active Directory Authentication Library (adal.min.js) を参照するための `<script>` 要素 - ADAL とも呼ばれ、API にアクセスするための OAuth 2.0 認証 (サインイン) とトークン取得を提供する JavaScript ライブラリです。
     - スタイル シート (sampleStyles.css、tsiclient.css) の `<link>` 要素 - CSS とも呼ばれ、色、フォント、間隔などのビジュアルなページ スタイルの詳細を制御するために使用されます。 
     - TSI Client ライブラリ (tsiclient.js) を参照するための `<script>` 要素 - TSI サービス API を呼び出してページ上のグラフ コントロールをレンダリングするためにページによって使用される JavaScript ライブラリです。

     >[!NOTE]
     > ADAL JavaScript ライブラリのソース コードは、[azure-activedirectory-library-for-js リポジトリ](https://github.com/AzureAD/azure-activedirectory-library-for-js)から入手できます。  
     > TSI Client JavaScript ライブラリのソース コードは、[tsiclient リポジトリ](https://github.com/Microsoft/tsiclient)から入手できます。

   - `<body>` の下には、ページ上の項目のレイアウトを定義するコンテナーとして機能する `<div>` 要素と、別の `<script>` 要素があります。
     - 最初の `<div>` は、"ログイン" ダイアログ (`id="loginModal"`) を指定します。
     - 2 番目の `<div>` は、以下の要素の親として機能します。
       - ページの上部のステータス メッセージとサインイン情報 (`class="header"`) のために使用されるヘッダー `<div>`。
       - すべてのグラフ (`class="chartsWrapper"`) を含む、残りのページ本文要素のための `<div>`。
       - ページを制御するために使用されるすべての JavaScript が含まれている `<script>` セクション。

   [![DevTools での TSI Client Sample](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. `<div class="chartsWrapper">` 要素を展開すると、各グラフ コントロールの例を配置するために使用される、より多くの子 `<div>` 要素が表示されます。 いくつかの `<div>` 要素のペアがあることに注意してください。それぞれが各グラフ例に対応しています。
   - 1 つ目 (`class="rowOfCardsTitle"`) には、グラフが何を表しているかを要約する、わかりやすいタイトルが含まれています。 たとえば、[Static Line Charts With Full-Size Legends]\(フルサイズの凡例付きの静的な折れ線グラフ\) です。
   - 2 つ目 (`class="rowOfCards"`) は、実際のグラフ コントロールを行内に配置する追加の子 `<div>` 要素を含む親です。 

  ![本文の "divs" の表示](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. 次に、`<div class="chartsWrapper">` 要素のすぐ下の `<script type="text/javascript">` 要素を展開します。 認証、TSI サービス API の呼び出し、グラフ コントロールのレンダリングなど、すべてのページ ロジックを処理するために使用される、ページ レベルの JavaScript セクションの先頭が表示されます。

  ![本文スクリプトの表示](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-client-javascript-library-concepts"></a>TSI Client JavaScript ライブラリの概念

詳細については説明しませんが、TSI Client ライブラリ (tsclient.js) は基本的に次の 2 つの重要なカテゴリの抽象化を提供します。

- **TSI Query API を呼び出すためのラッパー メソッド** - 集計式を使用して TSI データを照会できるようにし、ライブラリの `TsiClient.Server` 名前空間の下に編成されている REST API。 
- **いくつかの種類のグラフ コントロールを作成してデータを設定するためのメソッド** - TSI 集計データを Web ページにレンダリングするために使用され、ライブラリの `TsiClient.UX` 名前空間の下に編成されています。 

以下の概念は普遍的であり、一般的には TSI Client ライブラリ API に適用することができます。 

### <a name="authentication"></a>認証

前述のように、このサンプルは単一ページ アプリケーションであり、ユーザー認証に ADAL の OAuth 2.0 サポートを使用します。 スクリプトのこのセクションでは、次のような点に注目します。

1. 認証に ADAL を使用するには、クライアント アプリケーションが Azure Active Directory (Azure AD) のアプリケーション レジストリにそれ自体を登録する必要があります。 SPA として、このアプリケーションは "暗黙的な" OAuth 2.0 承認許可フローを使用するように登録されます。 これに対応して、アプリケーションは実行時に、クライアント ID GUID (`clientId`) やリダイレクト URI (`postLogoutRedirectUri`) などの一部の登録プロパティを、フローに参加するように指定します。

2. その後、アプリケーションは Azure AD に "アクセス トークン" を要求します。 アクセス トークンは、特定のサービス/API 識別子 (https://insights.timeseries.azure.com)、トークン "オーディエンス" とも呼ばれます) に対して、アクセス許可の有限集合として発行されます。 トークンのアクセス許可は、サインインしたユーザーの代わりに発行されます。 サービス/API の識別子は、アプリケーションの Azure AD 登録に含まれる、もう 1 つのプロパティです。 ADAL からアプリケーションに返されたアクセス トークンは、TSI サービス API にアクセスするときに、"ベアラー トークン" として渡されます。 

   [!code-javascript[head-sample](~/samples-javascript/pages/index.html?range=140-199&highlight=4-9,36-39)]

### <a name="control-identification"></a>コントロールの識別

前に説明したように、`<body>` 内の `<div>` 要素は、ページに表示されるすべてのグラフ コントロールのレイアウトを提供します。 各要素は、`id` プロパティも含めて、グラフ コントロールの配置とビジュアル属性のプロパティを指定します。 `id` プロパティは、一意の識別子を提供します。この識別子は、レンダリングおよび更新する各コントロールを識別したりバインドしたりするために、JavaScript コードで使用されます。 

### <a name="aggregate-expressions"></a>集計式

TSI Client ライブラリ API は、集計式を頻繁に使用します。 集計式は、1 つまたは複数の "検索語句" を構築する機能を提供します。 API は、[Time Series Insights エクスプローラー](https://insights.timeseries.azure.com/demo)が検索範囲、where 述語、メジャー、および分割用の値を使用する方法と似ています。 ほとんどのライブラリ API は、集計式の配列を受け取ります。この配列は、サービスが TSI データ クエリを構築するために使用します。

### <a name="call-pattern"></a>呼び出しパターン

グラフ コントロールのデータ設定とレンダリングは、一般的なパターンに従います。 このパターンは、ページ JavaScript 全体で使用されており、TSI Sample Application コントロールをインスタンス化して読み込みます。

1. 1 つまたは複数の TSI 集計式を保持するための配列を宣言します。  

   ```javascript
   var aes =  [];
   ```

2. 1 から n 個の集計式オブジェクトを構築し、それらを集計式配列に追加します。  

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```
   **aggregateExpression パラメーター**

   | パラメーター | [説明] | 例 |
   | --------- | ----------- | ------- |
   | predicateObject | データのフィルター式。 |`{predicateString: "Factory = 'Factory3'"}` |
   | measureObject   | 使用されているメジャーのプロパティ名。 | `{property: 'Temperature', type: "Double"}` |
   | measureTypes    | メジャー プロパティの望ましい集計。 | `['avg', 'min']` |
   | searchSpan      | 集計式の期間と間隔のサイズ。 | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | splitByObject   | 分割用に使用する文字列プロパティ (省略可能 – null にすることができます)。 | `{property: 'Station', type: 'String'}` |
   | color           | レンダリングするオブジェクトの色。 | `'pink'` |
   | alias           | 集計式のフレンドリ名。 | `'Factory3Temperature'` |
   | contextMenuActions | 視覚化時に時系列オブジェクトにバインドするアクションの配列 (省略可能)。 | 「高度な機能」セクションの「[ポップアップ コンテキスト メニュー](#popup-context-menus)」を参照してください。 |

3. `TsiClient.Server` API を使用して TSI クエリを呼び出し、集計データを要求します。  

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```
   **getAggregates パラメーター**

   | パラメーター | [説明] | 例 |
   | --------- | ----------- | ------- |
   | token     | TSI API のアクセス トークン | `authContext.getTsiToken()` [認証に関するセクション](#authentication)を参照してください。 |
   | envFQDN     | TSI 環境の完全修飾ドメイン名 | Azure portal での例 `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com` |
   | aeTsxArray | TSI クエリ式の配列 | 前に説明したように、`aes` 変数を使用します: `aes.map(function(ae){return ae.toTsx()}` |

4. TSI Query から返された圧縮された結果を、可視化のために JSON に変換します。

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. `TsiClient.UX` API を使用してグラフ コントロールを作成し、ページ上のいずれかの `<div>` 要素にバインドします。

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. 変換された JSON データ オブジェクトをグラフ コントロールに取り込み、ページ上にレンダリングします。

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>コントロールのレンダリング

ライブラリは、現在、8 個のユニークな分析コントロールを公開しています。 それらは、折れ線グラフ、円グラフ、横棒グラフ、ヒートマップ、階層コントロール、アクセス可能なグリッド、離散イベント タイムライン、および状態遷移タイムラインです。   

### <a name="line-bar-pie-chart-examples"></a>折れ線、横棒、円グラフの例 

最初に、アプリケーションで表示されるいくつかの標準的なグラフ コントロールの背後のコードと、それらを作成するためのプログラミング モデル/パターンを見てみましょう。 具体的には、`// Example 3/4/5` コメントの下の HTML セクションを調べます。このセクションでは、ID 値 `chart3`、`chart4`、`chart5` のコントロールがレンダリングされます。 

「[ページのソースと構造](#page-source-and-structure)」セクションの手順 3. で説明したように、グラフ コントロールはページ上の行に配置され、それぞれに説明的なタイトル行があります。 この例では、データを設定される 3 つのグラフはすべて、[Multiple Chart Types From the Same Data]\(同じデータでの複数のグラフの種類\) タイトル `<div>` の下にあり、その下の 3 つの `<div>` 要素にバインドされています。

[!code-javascript[code-sample1-line-bar-pie](~/samples-javascript/pages/index.html?range=60-74&highlight=1,5,9,13)]

JavaScript コードの次のセクションでは、前述のパターンを使用して TSI 集計式を作成し、TSI データのクエリに使用して、3 つのグラフをレンダリングします。 `tsiClient.ux` 名前空間で使用される 3 つの種類 (`LineChart`、`BarChart`、`PieChart`) に注意してください。それぞれのグラフが作成され、レンダリングされます。 また、3 つのすべてのグラフが、同じ集計式データ `transformedResult` を使用できることにも注意してください。

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/index.html?range=236-257&highlight=13-14,16-17,19-20)]

3 つのグラフは、レンダリングされると次のように表示されます。

[![同じデータの複数のグラフの種類](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>高度な機能

ライブラリは、利用する価値がありそうないくつかのオプションの高度な機能も公開しています。  

### <a name="states-and-events"></a>状態とイベント

提供される高度な機能の 1 つの例は、状態遷移と離散イベントをグラフに追加する機能です。 この機能は、インシデント、アラート、オン/オフなどの状態切り替えを強調表示するために便利です。 

ここでは、`// Example 10` コメントの下にある HTML のセクションの背後にあるコードを見ていきます。 このコードは、[Line Charts with Multiple Series Types]\(複数の系列の種類を含む折れ線グラフ\) というタイトルの下にある折れ線コントロールをレンダリングし、それを ID 値が `chart10` である `<div>` にバインドします。

1. 最初に、追跡する state-change 要素を保持するために、`events4` という名前の構造体が定義されます。 その構成要素を次に示します。
   - `"Component States"` という名前の文字列キー 
   - 状態を表す値オブジェクトの配列。それぞれに以下のものが含まれています。
     - JavaScript ISO タイムスタンプを含む文字列キー
     - 状態の特性を含む配列
       - 色
       - 説明

2. 次に、`events5` 構造が `"Incidents"` のために定義され、追跡されるイベント要素の配列を保持します。 配列構造体は、`events4` 用に概要が説明されているものと同じ形です。 

3. 最後に、2 つの構造体がグラフ オプション パラメーターの `events:` および `states:` と共に渡されて、折れ線グラフがレンダリングされます。 他のオプション パラメーターも注意してください。`tooltip:`、`theme:`、または `grid:` を指定できます。 

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/index.html?range=332-384&highlight=5,26,51)]

視覚的には、ダイヤモンド マーカー/ポップアップはインシデントを示すために使用され、時間スケールに沿った色付きのバー/ポップアップは状態の変化を示します。

[![複数の系列の種類を含む折れ線グラフ](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="popup-context-menus"></a>ポップアップ コンテキスト メニュー

高度な機能のもう 1 つの例として、カスタム コンテキスト メニュー (右クリックのポップアップ メニュー) があります。このメニューは、アプリケーションのスコープ内でアクションと論理的な次のステップを有効にするのに便利です。

ここでは、`// Example 13/14/15` の下の HTML の背後にあるコードを見て行きます。 このコードでは、最初に、[Line Chart with Context Menu to Create Pie/Bar Chart]\(円/棒グラフを作成するためのコンテキスト メニューを含む折れ線グラフ\) というタイトルの下に折れ線グラフをレンダリングし、ID 値が `chart13` の `<div>` 要素にバインドします。 折れ線グラフは、コンテキスト メニューを使用して、ID が `chart14` および `chart15` の `<div>` 要素にバインドされた円グラフおよび棒グラフを動的に作成する機能を提供します。 さらに、円グラフと棒グラフも、コンテキスト メニューを使用して独自の機能を有効にします。円グラフから棒グラフへのデータのコピーと、ブラウザー コンソール ウィンドウへの棒グラフ データの出力が可能です。

1. まず、一連のカスタム アクションが定義されます。 それぞれに、1 つ以上の要素を持つ配列が含まれています。各要素は、1 つのコンテキスト メニュー項目を定義します。 
   - `barChartActions`: 円グラフのコンテキスト メニューを定義します。1 つの項目を定義する 1 つの要素が含まれています。
     - `name`: メニュー項目 [Print parameters to console]\(コンソールへのパラメーターの出力\) のために使用されるテキスト
     - `action`: メニュー項目に関連付けられたアクション。これは常に、グラフを作成するために使用される集計式に基づいて 3 つの引数を受け取る匿名関数です。 この場合、それらはブラウザーのコンソール ウィンドウに書き込まれます。
       - `ae`: 集計式の配列
       - `splitBy`: splitBy 値
       - `timestamp`: タイムスタンプ
   - `pieChartActions`: 棒グラフのコンテキスト メニューを定義します。1 つの項目を定義する 1 つの要素が含まれています。 形とスキーマは前の `barChartActions` と同じですが、棒グラフをインスタンス化してレンダリングするので、`action` 関数は大きく異なっています。 また、この関数は `ae` 引数を使用して、実行時にメニュー項目をポップアップする際に渡される集計式配列を指定することにも注意してください。 さらにこの関数は、`ae.contextMenu` プロパティに `barChartActions` コンテキスト メニューを設定します。
   - `contextMenuActions`: 折れ線グラフのコンテキスト メニューを定義します。3 つのメニュー項目を定義する 3 つの要素が含まれています。 各要素の形とスキーマは、前の要素と同じです。 `barChartActions` と同様に、最初の項目は 3 つの関数引数をブラウザーのコンソール ウィンドウに書き込みます。 `pieChartActions` のように、2 番目の 2 つの項目は、それぞれ円グラフと棒グラフをインスタンス化してレンダリングします。 2 番目の 2 つの項目は、さらに `ae.contextMenu` プロパティにそれぞれ `pieChartActions` および `barChartActions` のコンテキスト メニューを設定します。

2. 次に、2 つの集計式が `aes` 集計式配列にプッシュされ、それぞれに `contextMenuActions` 配列が指定されます。 これらは、折れ線グラフ コントロールで使用されます。

3. 最後に、折れ線グラフのみが最初にレンダリングされます。円グラフと棒グラフのどちらも、実行時に折れ線グラフから表示できます。

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/index.html?range=456-535&highlight=7,16,29,61-64,78)]

スクリーン ショットでは、グラフが示されており、それぞれのポップアップ コンテキスト メニューが表示されています。 円グラフと棒グラフは、折れ線グラフのコンテキスト メニュー オプションを使用して、動的に作成されました。

[![円/棒グラフを作成するためのコンテキスト メニューを含む折れ線グラフ](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>ブラシ

ズームや調査などのアクションの定義で、時間範囲を特定するために、ブラシを使用することができます。 

ブラシを説明するためのコードは、前の「[ポップアップ コンテキスト メニュー](#popup-context-menus-section)」の "円/棒グラフを作成するためのコンテキスト メニューを含む折れ線グラフ" の例にも示されています。 

1. ブラシのアクションは、コンテキスト メニューと非常によく似ており、ブラシに一連のカスタム アクションを定義します。 それぞれに、1 つ以上の要素を持つ配列が含まれています。各要素は、1 つのコンテキスト メニュー項目を定義します。
   - `name`: メニュー項目 [Print parameters to console]\(コンソールへのパラメーターの出力\) のために使用されるテキスト
   - `action`: メニュー項目に関連付けられたアクション。これは常に 2 つの引数を受け取る匿名関数です。 この場合、それらはブラウザーのコンソール ウィンドウに書き込まれます。
      - `fromTime`: ブラシの選択範囲の "開始" タイムスタンプ
      - `toTime`: ブラシの選択範囲の "終了" タイムスタンプ

2. ブラシのアクションは、別のグラフ オプション プロパティとして追加されます。 `brushContextMenuActions: brushActions` プロパティが `linechart.Render` 呼び出しに渡されることに注意してください。

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/index.html?range=521-535&highlight=1,13)]

![ブラシを使用して円/棒グラフを作成するためのコンテキスト メニューを含む折れ線グラフ](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * TSI Sample アプリケーションにサインインしてそのソースを調べる
> * TSI JavaScript クライアント ライブラリの API を使用する
> * JavaScript を使用してグラフ コントロールを作成し、TSI データを設定する

説明したように、TSI Sample アプリケーションは、デモのデータ セットを使用します。 独自の TSI 環境とデータ セットを作成する方法の詳細については、次の記事に進んでください。

> [!div class="nextstepaction"]
> [Azure Time Series Insights 環境の計画](time-series-insights-environment-planning.md)


