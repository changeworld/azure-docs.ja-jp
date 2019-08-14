---
title: チュートリアル:Azure Time Series Insights JavaScript クライアント ライブラリを調べる | Microsoft Docs
description: Azure Time Series Insights JavaScript クライアント ライブラリと、関連するプログラミング モデルについて学習するためのチュートリアル。
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 3e56792673c33ac641ce23213f2b3662b889ec2a
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725660"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>チュートリアル:Azure Time Series Insights JavaScript クライアント ライブラリを調べる

JavaScript D3 ベースの Azure Time Series Insights クライアント ライブラリは、Time Series Insights に格納されたデータを照会して視覚化する Web 開発者を支援するために、開発されました。 このチュートリアルでは、ホストされているサンプル アプリを使用して、Time Series Insights クライアント ライブラリとプログラミング モデルについて説明します。

このチュートリアルでは、ライブラリの操作方法、Time Series Insights データへのアクセス方法、グラフ コントロールを使用したデータのレンダリングおよび視覚化方法について詳しく説明します。 また、さまざまな種類のグラフを試してデータを視覚化する方法についても説明します。 このチュートリアルの最後には、クライアント ライブラリを使用して、独自の Web アプリに Time Series Insights の機能を組み込むことができるようになります。

特に次の内容を学習します。

> [!div class="checklist"]
> * Time Series Insights のサンプル アプリケーション
> * Time Series Insights JavaScript クライアント ライブラリ
> * サンプル アプリケーションでライブラリを使用して Time Series Insights データを視覚化する方法

> [!NOTE]
> * このチュートリアルでは、ホストされている無料の [Time Series Insights Web デモ](https://insights.timeseries.azure.com/clientsample)を使用します。
> * Time Series Insights サンプル アプリのソース ファイルは、[GitHub サンプル リポジトリ](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)に用意されています。
> * [Time Series Insights クライアント](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)のリファレンス ドキュメントを参照してください。

お持ちでない場合は、[無料の Azure サブスクリプション](https://azure.microsoft.com/free/)にサインアップしてください。

## <a name="prerequisites"></a>前提条件

* このチュートリアルでは、ブラウザーの**開発者ツール**機能を使用します。 最新の Web ブラウザー ([Microsoft Edge](/microsoft-edge/devtools-guide)、[Chrome](https://developers.google.com/web/tools/chrome-devtools/)、[FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools)、[Safari](https://developer.apple.com/safari/tools/)、その他) では、通常、キーボード上の F12 ホット キーを使用して **Web インスペクター ビュー**にアクセスできます。 ビューにアクセスする別の方法として、Web ページを右クリックして **[要素の検査]** を選択します。

## <a name="review-video"></a>ビデオの視聴

このビデオでは、オープン ソースの Time Series Insights JavaScript SDK を紹介しています。
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="sample-application"></a>サンプル アプリケーション

このチュートリアルでは、ホストされている無料の Time Series Insights サンプル アプリを使用して、アプリケーションの背後にあるソース コードと Time Series Insights JavaScript クライアント ライブラリを調べます。 サンプル アプリを使用して、JavaScript で Time Series Insights を操作する方法と、チャートやグラフを使ってデータを視覚化する方法を学習します。

1. [Time Series Insights のサンプル アプリケーション](https://insights.timeseries.azure.com/clientsample)に移動します。 次のサインイン プロンプトが表示されます。

   [![Time Series Insights クライアント サンプル サインイン プロンプト](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. **[Log in]** を選択し、資格情報を入力または選択します。 エンタープライズ組織アカウント (Azure Active Directory) または個人アカウント (Microsoft アカウント) を使用します。

   [![Time Series Insights クライアント サンプル資格情報プロンプト](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. サインインした後、Time Series Insights データが設定されたグラフを表示するページが表示されます。 右上隅にユーザー アカウントと **[Log out]** オプションが表示されています。

   [![サインイン後の Time Series Insights クライアント サンプルのメイン ページ](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>ページのソースと構造

最初に、レンダリングされた Web ページの [HTML と JavaScript のソース コード](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html)を確認します。

1. ブラウザーで**開発者ツール**を開きます。 現在のページを構成する HTML 要素 (HTML ツリーまたは DOM ツリーとも呼ばれます) を調べます。

1. `<head>` と `<body>` 要素を展開し、次のセクションを確認します。

   * `<head>` 要素の下に、アプリの実行を可能にするページのメタデータと依存関係があります。
     * Azure Active Directory 認証ライブラリ (ADAL) ファイル *adal.min.js* を参照するために使用される `<script>` 要素。 ADAL は、OAuth 2.0 の認証 (サインイン) および API にアクセスするためのトークンの取得を提供する JavaScript ライブラリです。
     * *sampleStyles.css* や *tsiclient.css* など、スタイル シート (*CSS* とも呼ばれます) のための複数の `<link>` 要素。 スタイル シートは、色、フォント、間隔など、ビジュアル ページのスタイルの詳細を制御します。
     * Time Series Insights JavaScript クライアント ライブラリ *tsiclient.js* を参照するために使用される `<script>` 要素。 ページではライブラリを使用して Time Series Insights サービス API が呼び出され、ページ上のグラフ コントロールがレンダリングされます。

     >[!NOTE]
     > * ADAL JavaScript ライブラリのソース コードは、[azure-activedirectory-library-for-js リポジトリ](https://github.com/AzureAD/azure-activedirectory-library-for-js)で入手できます。
     > * Time Series Insights JavaScript クライアント ライブラリのソース コードは、[tsiclient リポジトリ](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)で入手できます。

   * `<body>` 要素の下に、ページ上の項目のレイアウトの定義に役立つ `<div>` 要素と、別の `<script>` 要素があります。
     * 最初の `<div>` 要素は、**ログイン** ダイアログ ボックス (`id="loginModal"`) を指定します。
     * 2 番目の `<div>` 要素は、以下の要素の親として機能します。
       * ページの上部のステータス メッセージとサインイン情報 (`class="header"`) のために使用されるヘッダー `<div>` 要素。
       * グラフ (`class="chartsWrapper"`) を含む、残りのページ本文要素のための `<div>` 要素。
       * ページを制御するために使用される JavaScript が含まれる `<script>` セクション。

   [![開発者ツールで表示した Time Series Insights クライアント サンプル](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. `<div class="chartsWrapper">` 要素を展開すると、子の `<div>` 要素がさらに表示されます。 これらの要素は、各グラフ コントロールの例の配置に使用されます。 `<div>` 要素のいくつかのペアがあります。それぞれが各グラフ例に対応しています。

   * 1 つ目の (`class="rowOfCardsTitle"`) 要素には、グラフが何を表しているかを要約する、わかりやすいタイトルが含まれています。 次に例を示します。`Static Line Charts With Full-Size Legends.`
   * 2 つ目の (`class="rowOfCards"`) 要素は、実際のグラフ コントロールを行内に配置する追加の子 `<div>` 要素を含む親です。

   [![本文の div 要素](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. `<div class="chartsWrapper">` 要素のすぐ下の `<script type="text/javascript">` 要素を展開します。 ページ レベルの JavaScript セクションの先頭部分は、すべてのページ ロジック (認証、Time Series Insights サービス API の呼び出し、グラフ コントロールのレンダリングなど) を処理するために使用されます。

   [![本文のスクリプト](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="javascript-client-library"></a>JavaScript クライアント ライブラリ

Time Series Insights クライアント ライブラリ (*tsiclient.js*) は、2 つの重要な JavaScript 機能用の抽象化を提供します。

* **Time Series Insights Query API を呼び出すためのラッパー メソッド**: 集計式を使用して Time Series Insights データを照会するために使用できる REST API。 メソッドは、ライブラリの TsiClient.Server 名前空間の下に整理されています。

* **いくつかの種類のグラフ コントロールを作成してデータを設定するためのメソッド**: Web ページでの Time Series Insights 集計データのレンダリングに使用できるメソッド。 メソッドは、ライブラリの TsiClient.UX 名前空間の下に整理されています。

これらの簡略化によって、開発者は Time Series Insights データを利用した UI グラフやチャート コンポーネントをより簡単に構築できます。

### <a name="authentication"></a>Authentication

[Time Series Insights のサンプル アプリケーション](https://insights.timeseries.azure.com/clientsample)は、ADAL OAuth 2.0 ユーザー認証サポートのある単一ページ アプリです。

1. 認証に ADAL を使用する場合は、Azure Active Directory (Azure AD) にクライアント アプリを登録する必要があります。 実際には、この単一ページ アプリは [OAuth 2.0 の暗黙的な許可フロー](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow)を使用するように登録されます。
1. アプリケーションで、実行時に登録プロパティの一部を指定する必要があります。 プロパティには、クライアント GUID (`clientId`) とリダイレクト URI (`postLogoutRedirectUri`) が含まれます。
1. その後、アプリは Azure AD に "*アクセス トークン*" を要求します。 アクセス トークンは、特定のサービスまたは API 識別子 (https:\//api.timeseries.azure.com) に対して、アクセス許可の有限集合として発行されます。 トークンのアクセス許可は、サインインしたユーザーの代わりに発行されます。 サービスまたは API の識別子は、アプリの Azure AD 登録に含まれている別のプロパティです。
1. ADAL からアプリに返されたアクセス トークンは、Time Series Insights サービス API にアクセスするときに、"*ベアラー トークン*" として渡されます。

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Microsoft がサポートする Azure AD 認証ライブラリの詳細については、[Azure Active Directory 認証ライブラリのリファレンス ドキュメント](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries)をご覧ください。

### <a name="control-identification"></a>コントロールの識別

示されている例では、`<div>` 要素が親の`<body>` 要素内に配置され、ページ上にレンダリングされるグラフ コントロールに適切なレイアウトを提供します。

各 `<div>` 要素では、グラフ コントロールの配置とビジュアル属性のプロパティを指定します。 HTML 要素 `id` プロパティは、視覚化されたデータのレンダリングと更新のために特定のコントロールにバインドするための一意の識別子として機能します。

### <a name="aggregate-expressions"></a>集計式

Time Series Insights クライアント ライブラリ API は、集計式を使用します。

* 集計式には、1 つ以上の*検索語句*を構築する機能があります。

* クライアント API は、検索範囲、`where` 述語、メジャー、および `splitBy` 値を使用する別提供のデモ アプリ ([Time Series Insights エクスプローラー](https://insights.timeseries.azure.com/demo)) と同様の機能を提供するように設計されています。

* ほとんどのクライアント ライブラリ API は、Time Series Insights データ クエリを作成するためにサービスによって使用される集計式の配列を受け取ります。

### <a name="call-pattern"></a>呼び出しパターン

グラフ コントロールのデータ設定とレンダリングは、一般的なパターンに従います。 一般的なパターンは、サンプル アプリ全体で確認でき、クライアント ライブラリを使用するときに役立ちます。

1. 1 つまたは複数の Time Series Insights 集計式を保持するための `array` を宣言します。

   ```javascript
   var aes =  [];
   ```

1. *1* から *n* 個の集計式オブジェクトを作成します。 次に、それらを集計式の配列に追加します。

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **aggregateExpression パラメーター**

   | パラメーター | 説明 | 例 |
   | --------- | ----------- | ------- |
   | `predicateObject` | データのフィルター式 |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | 使用されているメジャーのプロパティ名 | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | 必要なメジャー プロパティの集計 | `['avg', 'min']` |
   | `searchSpan`      | 集計式の期間と間隔のサイズ | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | 分割用に使用する文字列プロパティ (省略可能: null にすることができます) | `{property: 'Station', type: 'String'}` |
   | `color`         | レンダリングするオブジェクトの色 | `'pink'` |
   | `alias`           | 集計式のフレンドリ名 | `'Factory3Temperature'` |
   | `contextMenuActions` | 視覚エフェクト内で時系列オブジェクトにバインドするアクションの配列 (省略可能) | 詳細については、「[ポップアップ コンテキスト メニュー](#pop-up-context-menus)」をご覧ください。 |

1. TsiClient.Server API を使って Time Series Insights クエリを呼び出して、集計データを要求します。

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates パラメーター**

   | パラメーター | 説明 | 例 |
   | --------- | ----------- | ------- |
   | `token`     | Time Series Insights API のアクセス トークン |  `authContext.getTsiToken()`<br />詳細については、[認証](#authentication)に関するページをご覧ください。 |
   | `envFQDN`   | Time Series Insights 環境の完全修飾ドメイン名 (FQDN) | Azure portal を使用します。 (例: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`)。 |
   | `aeTsxArray` | Time Series Insights クエリ式の配列 | 前に説明したように、`aes` 変数を使用します。`aes.map(function(ae){return ae.toTsx()}` |

1. Time Series Insights クエリから返された圧縮された結果を、可視化のために JSON に変換します。

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. TsiClient.UX API を使用してグラフ コントロールを作成します。 それをページ上のいずれかの `<div>` 要素にバインドします。

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. 変換された JSON データ オブジェクトをグラフ コントロールに取り込み、ページにコントロールをレンダリングします。

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>コントロールのレンダリング

Time Series Insights クライアント ライブラリには、すぐに使用できる固有の分析コントロールが 8 つ用意されています。

* **折れ線グラフ**
* **円グラフ**
* **横棒グラフ**
* **heatmap**
* **階層コントロール**
* **アクセス可能なグリッド**
* **離散イベント タイムライン**
* **状態遷移タイムライン**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>折れ線グラフ、横棒グラフ、および円グラフの例

標準のグラフ コントロールの一部をレンダリングするために使用するデモ コードを確認します。 それらのコントロールを作成するためのプログラミング モデルとパターンに注目してください。 具体的には、`// Example 3/4/5` コメントの下の HTML を調べます。ここで、HTML `id` 値 `chart3`、`chart4`、`chart5` のコントロールがレンダリングされます。

[「ページのソースと構造」セクション](#page-source-and-structure)の手順 3 で説明したように、グラフ コントロールはページ上の行に配置されます。 各グラフ コントロールに説明的なタイトル行があります。 この例では、3 つのグラフは `Multiple Chart Types From the Same Data` というタイトルの `<div>` 要素の下にあり、タイトルの下の 3 つの `<div>` 要素にバインドされています。

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

JavaScript コードの次のセクションでは、前述のパターンを使用して Time Series Insights 集計式を作成し、Time Series Insights データのクエリに使用して、3 つのグラフをレンダリングします。 3 つのグラフの種類 (`LineChart`、`BarChart`、`PieChart`) が tsiClient.ux 名前空間から使用されます。 グラフの種類は、それぞれのグラフの作成とレンダリングに使用されます。 3 つのすべてのグラフが、同じ集計式データ `transformedResult` を使用できます。

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

3 つのグラフは、レンダリングされると次のように表示されます。

[![同じデータから作成される複数の種類のグラフ](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="learn-about-advanced-features"></a>高度な機能の学習

Time Series Insights クライアント ライブラリには、データ視覚化を独創的に実装するために使用できるいくつかの追加機能があります。

### <a name="states-and-events"></a>状態とイベント

高度な機能の 1 つは、状態遷移と離散イベントをグラフに追加する機能です。 この機能は、インシデントの強調表示、アラート、状態切り替えの作成 (オン/オフのスイッチなど) を行うのに役立ちます。

`// Example 10` コメントの周囲のコードを確認します。 このコードは、`Line Charts with Multiple Series Types` というタイトルの下にある折れ線コントロールをレンダリングし、それを HTML `id` 値が `chart10` である `<div>` 要素にバインドします。

次の手順では、プロセスを説明します。

1. 追跡する state-change 要素を保持するために、`events4` という名前の構造体が定義されます。構造体には次のものが含まれています。

   * `Component States` という名前の文字列キー。
   * 状態を表す値オブジェクトの配列。 各オブジェクトには以下のものが含まれます。
     * JavaScript ISO タイムスタンプを含む文字列キー。
     * 状態の特性 (色と説明) を含む配列。

1. `Incidents` に対する `events5` 構造体が定義されています。これは追跡するイベント要素の配列を保持します。配列構造体は、`events4` 用に概要が説明されている構造体と同じ形です。

1. 2 つの構造体がグラフ オプション パラメーターの `events:` および `states:` と共に渡されて、折れ線グラフがレンダリングされます。 他のオプション パラメーターも注意してください。`tooltip:`、`theme:`、または `grid:` を指定できます。

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

ダイヤモンド マーカー/ポップアップ ウィンドウはインシデントを示すために使用され、時間スケールに沿った色付きのバー/ポップアップ ウィンドウは状態の変化を示します。

[![複数の系列の種類を含む折れ線グラフ](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>ポップアップ コンテキスト メニュー

もう 1 つの高度な機能は、カスタム コンテキスト メニュー (右クリック ポップアップ メニュー) を作成する機能です。 カスタム コンテキスト メニューは、アプリケーションのスコープ内でアクションおよび次の論理的ステップを有効にするのに役立ちます。

`// Example 13/14/15` コメントの周囲のコードを確認します。 このコードは、タイトル `Line Chart with Context Menu to Create Pie/Bar Chart` の下の折れ線グラフを最初にレンダリングします。 グラフは、HTML `id` 値 `chart13` を持つ `<div>` 要素にバインドされます。

折れ線グラフは、コンテキスト メニューを使用して、ID が `chart14` および `chart15` の `<div>` 要素にバインドされた円グラフおよび棒グラフを動的に作成する機能を提供します。 円グラフと棒グラフも、コンテキスト メニューを使用して独自の機能を有効にします。円グラフから棒グラフへのデータのコピーと、ブラウザー コンソール ウィンドウへの棒グラフ データの出力が可能です。

次の手順では、プロセスを説明します。

1. 一連のカスタム アクションが定義されます。 各アクションには、1 つ以上の要素を含む配列が含まれています。 各要素は、1 つのコンテキスト メニュー項目を定義しています。

   * `barChartActions`:このアクションは、円グラフのコンテキスト メニューを定義します。1 つの項目を定義する 1 つの要素が含まれています。
     * `name`:メニュー項目 "Print parameters to console" に使用されるテキストです。
     * `action`:メニュー項目に関連付けられたアクションです。 アクションは常に、グラフを作成するために使用される集計式に基づいて 3 つの引数を受け取る匿名関数です。 この場合、引数はブラウザーのコンソール ウィンドウに書き込まれます。
       * `ae`:集計式の配列です。
       * `splitBy`:`splitBy` の値です。
       * `timestamp`:タイムスタンプです。

   * `pieChartActions`:このアクションは、棒グラフのコンテキスト メニューを定義します。1 つの項目を定義する 1 つの要素が含まれています。 形とスキーマは前述の `barChartActions` 要素と同じですが、棒グラフをインスタンス化してレンダリングするので、`action` 関数は大きく異なっています。 `ae` 引数が、実行時にメニュー項目が開くときに渡される集計式配列を指定するために使用されています。 さらにこの関数は、`ae.contextMenu` プロパティに `barChartActions` コンテキスト メニューを設定します。
   * `contextMenuActions`:このアクションは、折れ線グラフのコンテキスト メニューを定義します。3 つのメニュー項目を定義する 3 つの要素が含まれています。 各要素の形とスキーマは、前に説明した要素と同じです。 `barChartActions` 要素と同様に、最初の項目は 3 つの関数引数をブラウザーのコンソール ウィンドウに書き込みます。 `pieChartActions` 要素のように、2 番目の 2 つの項目は、それぞれ円グラフと棒グラフをインスタンス化してレンダリングします。 2 番目の 2 つの項目は、さらに `ae.contextMenu` プロパティにそれぞれ `pieChartActions` および `barChartActions` のコンテキスト メニューを設定します。

1. 2 つの集計式が `aes` 集計式配列にプッシュされます。 これらによって、各項目に `contextMenuActions` 配列が指定されます。 これらの式は、折れ線グラフ コントロールで使用されます。

1. 折れ線グラフのみが最初にレンダリングされます。円グラフと棒グラフのどちらも、実行時に折れ線グラフから表示できます。

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

次のスクリーンショットでは、グラフが示されており、それぞれのポップアップ コンテキスト メニューが表示されています。 円グラフと棒グラフは、折れ線グラフのコンテキスト メニュー オプションを使用して、動的に作成されました。

[![円グラフと棒グラフを作成するためのコンテキスト メニューを含む折れ線グラフ](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>ブラシ

ブラシを使用して、ズームや調査などのアクションを定義する時間範囲を特定できます。

ブラシを説明するために使用されるコードは、ポップアップ コンテキスト メニューを示す `Line Chart with Context Menu to Create Pie/Bar Chart` 例に示されています。

* ブラシのアクションは、コンテキスト メニューとよく似ており、ブラシに一連のカスタム アクションを定義します。 各アクションには、1 つ以上の要素を含む 1 つの配列が含まれています。 各要素は、1 つのコンテキスト メニュー項目を定義しています。
   * `name`:メニュー項目 "Print parameters to console" に使用されるテキストです。
   * `action`:メニュー項目に関連付けられたアクション。これは常に 2 つの引数を受け取る匿名関数です。 この場合、引数はブラウザーのコンソール ウィンドウに書き込まれます。
     * `fromTime`:ブラシの選択範囲の `from` タイムスタンプです。
     * `toTime`:ブラシの選択範囲の `to` タイムスタンプです。

* ブラシのアクションは、別のグラフ オプション プロパティとして追加されます。 `brushContextMenuActions: brushActions` プロパティが `linechart.Render` 呼び出しに渡されます。

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![ブラシを使用して円グラフと棒グラフを作成するためのコンテキスト メニューを含む折れ線グラフ](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルを完了したので、作成したリソースをクリーンアップします。

1. [Azure portal](https://portal.azure.com) の左側のメニューにある **[すべてのリソース]** を選択し、目的の Azure Time Series Insights リソース グループを見つけます。
1. **[削除]** を選択してリソース グループ全体 (およびその中に含まれるすべてのリソース) を削除するか、各リソースを個別に削除します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Time Series Insights サンプル アプリケーションにサインインしてそのソースを調べる
> * Time Series Insights JavaScript クライアント ライブラリで API を使用する
> * JavaScript を使用してグラフ コントロールに Time Series Insights データを設定する

Time Series Insights サンプル アプリケーションではデモ データセットを使用します。 独自の Time Series Insights 環境とデータ セットを作成する方法については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [チュートリアル:Azure Time Series Insights 環境を作成する](tutorial-create-populate-tsi-environment.md)

または、Time Series Insights サンプル アプリケーション ソース ファイルを表示します。

> [!div class="nextstepaction"]
> [Time Series Insights サンプル アプリのリポジトリ](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Time Series Insights クライアント API のリファレンス ドキュメントを参照します。

> [!div class="nextstepaction"]
> [Time Series Insights API リファレンス ドキュメント](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
