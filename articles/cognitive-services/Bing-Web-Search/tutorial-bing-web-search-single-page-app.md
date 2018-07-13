---
title: Bing Web Search シングルページ Web アプリ | Microsoft Docs
description: シングルページ Web アプリケーションで Bing Web Search API を使用する方法を説明します。
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: f22e38a1d6ee4042684b9822b58669bed6fe29a0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377032"
---
# <a name="tutorial-single-page-web-app"></a>チュートリアル: シングルページ Web アプリ

Bing Web Search API を使うと、Web を検索して、検索クエリの内容に該当するさまざまな種類の結果を取得できます。 このチュートリアルでは、Bing Web Search API を使用して検索結果をページ内に表示するシングルページ Web アプリケーションを構築します。 このアプリケーションには、HTML、CSS、JavaScript のコンポーネントが含まれます。

<!-- Remove until this can be replaced with a sanitized version.
![[Single-page Bing Web Search app]](media/cognitive-services-bing-web-api/web-search-spa-demo.png)
-->

> [!NOTE]
> ページの下部にある JSON と HTTP のヘッダーをクリックすると、JSON 応答と HTTP 要求の情報が表示されます。 これらの詳細情報は、サービスの詳細を調べるのに役立ちます。

このチュートリアル アプリは、次の方法を示しています。

> [!div class="checklist"]
> * Bing Web Search API 呼び出しを JavaScript で実行する
> * Bing Web Search API に検索オプションを渡す
> * Web、ニュース、画像、動画の検索結果を表示する
> * 検索結果をページングする
> * Bing のクライアント ID と API のサブスクリプション キーを処理する
> * 発生する可能性のあるエラーを処理する

このチュートリアルのページでは、すべてが自己完結しています。外部のフレームワーク、スタイル シートはもちろん、画像ファイルすら使用することはありません。 広くサポートされている JavaScript 言語機能のみを使用し、現在のバージョンの主要な Web ブラウザーすべてで動作します。

このチュートリアルでは、ソース コードの一部についてのみ説明します。 完全なソース コードは[別のページ](tutorial-bing-web-search-single-page-app-source.md)から入手できます。 このコードをコピーしてテキスト エディターに貼り付け、`bing.html` として保存してください。

## <a name="app-components"></a>アプリのコンポーネント

他のシングルページ Web アプリと同様に、このチュートリアル用アプリケーションには 3 つのパーツが含まれています。

> [!div class="checklist"]
> * HTML - ページの構造と内容を定義します
> * CSS - ページの外観を定義します
> * JavaScript - ページの動作を定義します

このチュートリアルでは、HTML と CSS の大部分について詳しく取り上げません。HTML と CSS はわかりやすいためです。

HTML には検索フォームが含まれ、ユーザーはこれにクエリを入力して検索オプションを選択します。 フォームは JavaScript に接続されます。JavaScript は、`<form>` タグの `onsubmit` 属性を使用して、実際に検索を実行します。

```html
<form name="bing" onsubmit="return newBingWebSearch(this)">
```

`onsubmit` ハンドラーは `false` を返すことで、フォームがサーバーに送信されないようにします。 実際には JavaScript コードがフォームから必要な情報を収集し、検索を実行する処理を行います。

HTML には、検索結果が表示されるブロック (HTML `<div>` タグ) も含まれます。

## <a name="managing-subscription-key"></a>サブスクリプション キーの管理

コードに Bing Search API サブスクリプション キーを含めなくてもよいように、ブラウザーの永続的ストレージを使用してキーを格納します。 キーが格納されていない場合は、後で使用するためにユーザーのキーを格納することを求めます。 後でキーが API によって拒否された場合は、格納されたキーを無効にし、ユーザーに再度操作を求めます。

`localStorage` オブジェクト (ブラウザーでサポートされている場合) または Cookie のいずれかを使用する `storeValue` 関数と `retrieveValue` 関数を定義します。 `getSubscriptionKey()` 関数は、これらの関数を使用してユーザーのキーを格納、取得します。

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

HTML の `form` タグの `onsubmit` は、検索結果を返す `bingWebSearch` 関数を呼び出します。 `bingWebSearch` は `getSubscriptionKey` を使用して各クエリを認証します。 前の定義のとおり、キーが入力されていない場合は、`getSubscriptionKey` はユーザーにキーの入力を求めます。 その後、アプリケーションで引き続き使用できるよう、キーが格納されます。

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>検索オプションの選択

![[Bing Web Search のフォーム]](media/cognitive-services-bing-web-api/web-search-spa-form.png)

この HTML のフォームには、次の名前の付いた要素が含まれます。

| | |
|-|-|
| `where` | 検索で使用される市場 (場所と言語) を選択するドロップダウン メニュー。 |
| `query` | 検索語句を入力するテキスト フィールド。 |
| `what` | 特定の種類の結果のレベルを上げる際に使用するチェック ボックス。 たとえば、画像のレベルを上げると、画像の順位が上がります。 |
| `when` | オプションとして、最近の日、週、または月に検索を限定するためのドロップダウン メニュー。 |
| `safe` | "成人向け" の結果をフィルターで除外する Bing のセーフサーチ機能を使用するかどうかを指定するチェック ボックス。 |
| `count` | 隠しフィールド。 各要求に対して返される検索結果の数。 変更すると、1 ページあたりの結果の表示数が増減します。 |
| `offset` | 隠しフィールド。 要求における最初の検索結果のオフセット。ページングに使用されます。 新しい要求では `0` にリセットされます。 |

> [!NOTE]
> Bing Web Search には、もっと多くのクエリ パラメーターがあります。 ここではその一部のみを使用しています。

JavaScript 関数 `bingSearchOptions()` は、これらのフィールドを、Bing Search API で必要とされる形式に変換します。

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

たとえば、実際の API 呼び出し内の `SafeSearch` パラメーターは、`strict`、`moderate`、`off` のいずれかにできます。既定値は `moderate` です。 ただし、フォームでは、2 つの状態しかないチェック ボックスを使用します。 JavaScript コードにより、この設定が `strict` または `off` のいずれかに変換されます (`moderate` は使用しません)。

**［レベル上げ］** チェック ボックスのいずれかをオンにすると、クエリに `answerCount` パラメーターも追加されます。 `answerCount` は `promote` パラメーターを使用する際に必要です。 これを単純に `9` (Bing Web Search API でサポートされている結果の種類の数) に設定して、最大限に多くの種類の結果を取得できるようにします。

> [!NOTE]
> 結果の種類をレベル上げしても、必ずしもその種類の結果が検索結果に含まれるとは "*限りません*"。 レベル上げの効果は、その種類の結果の順位を、通常の順位よりも上げるというものです。 検索結果を特定の種類だけに制限するには、`responseFilter` クエリ パラメーターを使用するか、Bing Image Search や Bing News Search など、より具体的なエンドポイントを呼び出します。

また、`textDecoration` および `textFormat` クエリ パラメーターも送信して、検索用語が検索結果内で太字で表示されるようにします。 これらの値は、スクリプトにハードコードされています。

## <a name="performing-the-request"></a>要求の実行

クエリ、オプションの文字列、および API キーを指定すると、`BingWebSearch` 関数は `XMLHttpRequest` オブジェクトを使用して Bing Web Search エンドポイントに要求を送信します。

```javascript
// perform a search given query, options string, and API key
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);
    
    // event handler for successful response
    request.addEventListener("load", handleBingResponse);
    
    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

HTTP 要求が正常に完了すると、JavaScript は API への HTTP GET 要求を正常に処理するために、`load` イベント ハンドラーと `handleBingResponse()` 関数を呼び出します。 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> HTTP 要求が成功しても、必ずしも検索自体が成功したとは "*限りません*"。 検索操作でエラーが発生した場合、Bing Web Search API は 200 以外の HTTP 状態コードを返し、JSON 応答にエラー情報を含めます。 また、要求のレートが制限されている場合、API は空の応答を返します。

先ほどの両方の関数内にあるコードの多くは、エラー処理専用のものです。 エラーは次のステージで発生する場合があります。

|段階|起こり得るエラー|処理元|
|-|-|-|
|JavaScript 要求オブジェクトのビルド|無効な URL|`try`/`catch` ブロック|
|要求の実行|ネットワーク エラー、接続の中止|`error` および `abort` イベント ハンドラー|
|検索の実行|無効な要求、無効な JSON、レート制限|`load` イベント ハンドラー内のテスト|

エラーは、`renderErrorMessage()` とエラーに関する既知の情報を呼び出すことで処理されます。 応答がエラー テストにすべてパスしたら、`renderSearchResults()` を呼び出して、ページ内に検索結果を表示します。

## <a name="displaying-search-results"></a>検索結果の表示

Bing Web Search API では、[指定した順序で結果を表示する必要があります](useanddisplayrequirements.md)。 API は異なる種類の応答を返す可能性があるため、JSON 応答内の最上位の `WebPages` コレクションを反復処理してそれらの結果を表示するだけでは不十分です  (1 つの種類の結果だけを取得したい場合は、`responseFilter` クエリ パラメーターまたは他の Bing Search エンドポイントを使用します)。

代わりに、検索結果で `rankingResponse` を使用して、表示する結果を順序付けします。 このオブジェクトは `WebPages`、`News`、`Images`、`Videos` コレクション内、または JSON 応答内のその他の最上位の応答コレクション内の項目を参照します。

`rankingResponse` には、検索結果の最大 3 つのコレクション (指定された `pole`、`mainline`、および `sidebar`) を含めることができます。 

`pole` (存在する場合) は最も関連性の高い検索結果であり、目立つように表示する必要があります。 `mainline` は検索結果の大部分を参照します。 メインラインの結果は、`pole` の直後に表示される必要があります (`pole` が存在しない場合は、最初に表示されます)。 

最後に、 `sidebar` は、補助検索結果を参照します。 多くの場合、これらの結果は、関連する検索や画像になります。 可能であれば、これらの結果は実際のサイドバーに表示する必要があります。 画面の制限によってサイドバーが使用できない場合は、(たとえばモバイル デバイスなど)、`mainline` の結果の後に表示する必要があります。

`rankingResponse` コレクション内の各項目は、2 つの異なる (ただし同等の) 方法で実際の検索結果の項目を参照します。

| | |
|-|-|
|`id`|`id` は URL に似ていますが、リンクには使用できません。 `id` 型のランキング結果は、応答コレクション内の検索結果項目、"*または*" 応答コレクション全体 (`Images` など) のいずれかの `id` と一致します。
|`answerType`、`resultIndex`|`answerType` は、結果を含む最上位の応答コレクション (`WebPages` など) を参照します。 `resultIndex` は、そのコレクション内の結果のインデックスを参照します。 `resultIndex` を省略すると、ランキング結果はコレクション全体を参照したものとなります。

> [!NOTE]
> 検索応答のこの部分の詳細については、[ランキング結果](rank-results.md)に関するページを参照してください。

どちらの方法も、アプリケーションで参照先の検索結果項目を特定するうえで非常に便利です。 このチュートリアルのコードでは、`answerType` と `resultIndex` を使用して各検索結果を特定します。

最後に、`renderSearchResults()` 関数について説明します。 この関数は、検索結果の 3 つのセクションを表す 3 つの `rankingResponse` コレクションを反復処理します。 セクションごとに `renderResultsItems()` を呼び出して、そのセクションの結果をレンダリングします。

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

`renderResultsItems()` は各 `rankingResponse` セクション内の項目を反復処理し、`answerType` および `resultIndex` フィールドを使用して各ランキング結果を検索結果にマッピングします。さらに、適切なレンダリング関数を呼び出して、結果の HTML を生成します。 

指定したランキング項目に `resultIndex` が指定されていない場合は、`renderResultsItems()` がその種類の結果すべてを反復処理し、各項目でレンダリング関数を呼び出します。 

どちらの場合でも、結果の HTML は、ページ内の適切な `<div>` 要素に挿入されます。

```javascript
// render search results from rankingResponse object in specified order
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` rankingResult type is in the `webPages` top-level collection
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        // must have results of the given type AND a renderer for it
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // this ranking item refers to ONE result of the specified type
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // this ranking item refers to ALL results of the specified type
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="rendering-result-items"></a>結果項目のレンダリング

JavaScript コードにはオブジェクト `searchItemRenderers` が存在します。これには *renderers:* 関数が含まれ、各種類の検索結果ごとに HTML を生成します。

```javascript
// render functions for various types of search results
searchItemRenderers = { 
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!NOTE]
> このチュートリアル アプリには、Web ページ、ニュース項目、画像、動画、関連する検索用のレンダラーが備わっています。 独自のアプリケーションでは、受け取る可能性のあるすべての種類の結果に対するレンダラーが必要です。結果の種類には、計算、スペル修正候補、エンティティ、タイム ゾーン、定義などがあります。

一部のレンダリング関数は、単一の検索結果を表す JavaScript オブジェクトである `item` パラメーターしか受け取りません。 異なるコンテキスト内で項目を別の方法でレンダリングするために使用できる他のパラメーターを受け取るものもあります  (この情報を使わないレンダラーは、これらのパラメーターを受け取る必要はありません)。

コンテキスト引数は次のとおりです。

| | |
|-|-|
|`section`|項目が表示される結果のセクション (`pole`、`mainline`、`sidebar`) です。
|`index`<br>`count`|特定のコレクション内のすべての結果が表示されるように `rankingResponse` 項目で指定されている場合に使用可能です。それ以外の場合は `undefined` を使用します。 これらのパラメーターでは、コレクション内の項目のインデックスと、そのコレクション内の項目の総数を受け取ります。 この情報を使用すると、結果の数をカウントしたり、最初または最後の結果について別の HTML を生成したりすることなどができます。|

このチュートリアル アプリでは、`images` および `relatedSearches` レンダラーの両方でコンテキスト引数を使用して、生成する HTML をカスタマイズします。 `images` レンダラーについて詳しく見ていきましょう。

```javascript
searchItemRenderers = { 
    // render image result using thumbnail
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    }, // other renderers omitted
}
```

この画像レンダラー関数は次の処理を行います。

> [!div class="checklist"]
> * 画像のサムネイルのサイズを計算します (高さは 60 ピクセルに固定されます)。
> * コンテキストに沿って、画像の結果の前に HTML を挿入します。
> * 画像を含むページにリンクする HTML `<a>` タグをビルドします。
> * 画像のサムネイルを表示する HTML `<img>` タグをビルドします。 

画像レンダラーは、`section` および `index` 変数を使用して、表示場所によって異なる方法で結果を表示します。 改行 (`<br>` タグ) はサイドバー内の画像の結果の間に挿入されます。これにより、サイドバーに画像が列になって表示されます。 他のセクション内では、最初の画像の結果である `(index === 0)` は `<p>` タグの前に挿入されます。 そうしないと、サムネイルが互いに干渉し合い、ブラウザー ウィンドウ内で必要に応じてラップしてしまいます。

サムネイルのサイズは、サムネイルの URL 内の `<img>` タグに加えて、`h` および `w` フィールドで使用されます。 その後、[Bing サムネイル サービス](resize-and-crop-thumbnails.md)から、そのサイズのとおりのサムネイルが提供されます。 `title` および `alt` 属性 (画像の説明テキスト) は、画像の名前と URL のホスト名で構成されます。

画像は、メインラインの検索結果に次のように表示されます。

![[Bing の画像の結果]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persisting-client-id"></a>クライアント ID の永続化

Bing Search API からの応答には、`X-MSEdge-ClientID` ヘッダーが含まれることがあり、このヘッダーは後続の要求で API に送り返す必要があります。 複数の Bing Search API を使用している場合、可能であれば、それらすべてで同じクライアント ID を使用する必要があります。

`X-MSEdge-ClientID` ヘッダーを提供すると、Bing API がユーザーのすべての検索を関連付けられるようになります。これには、2 つの重要な利点があります。

1 つ目は、Bing 検索エンジンが、検索に過去のコンテキストを適用できるようになることです。これにより、ユーザーがより満足できる結果を見つけることができます。 たとえば、ユーザーが過去に航海に関連する用語を検索したことがあった場合、その後の "ノット" という検索では、航海用語としてのノットに関する情報を優先的に返すことができます。

2 つ目は、Bing でユーザーをランダムに選択し、まだ広く利用されていない新機能を体験してもらえるようになることです。 各要求で同じクライアント ID を提供することで、機能の表示対象として選択されているユーザーには、常にそれが表示されるようになります。 クライアント ID を提供しないと、ユーザーには、検索結果内で機能が見かけ上ランダムに表示されたり消えたりする可能性があります。

ブラウザーのセキュリティ ポリシー (CORS) により、`X-MSEdge-ClientID` ヘッダーを JavaScript で使用できない場合もあります。 この制限は、検索応答のオリジンとその要求元のページとが異なる場合に生じます。 運用環境では、API 呼び出しを実行するサーバー側スクリプトを Web ページと同じドメインに ホストして、このポリシーに対応する必要があります。 スクリプトのオリジンが Web ページと同じになるので、`X-MSEdge-ClientID` ヘッダーを JavaScript で利用できます。

> [!NOTE]
> 運用環境の Web アプリケーションでは、いずれにしても要求サーバー側を実行する必要があります。 そうしないと、Web ページに Bing Search API キーを含める必要があり、ソースを表示した人ならだれでもキーを使用できるようになってしまいます。 権限のない者によって行われた要求であっても、すべての利用に対してユーザーの API サブスクリプション キーに基づいて料金が発生するため、キーは公開しないようにする必要があります。

開発目的の場合は、CORS プロキシを介して Bing Web Search API 要求を行うことができます。 このようなプロキシからの応答には、応答ヘッダーをホワイトリストに登録して JavaScript で使用できるようにする `Access-Control-Expose-Headers` ヘッダーが含まれています。

CORS プロキシをインストールして、チュートリアル アプリがクライアント ID ヘッダーにアクセスできるようにするのは簡単です。 まず、[Node.js をインストールします](https://nodejs.org/en/download/) (まだインストールしていない場合)。 さらに、コマンド ウィンドウで次のコマンドを発行します。

    npm install -g cors-proxy-server

次に、HTML ファイル内の Bing Web Search エンドポイントを次のように変更します。

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

最後に、次のコマンドを使用して、CORS プロキシを開始します。

    cors-proxy-server

チュートリアル アプリを使用している間はコマンド ウィンドウを開いたままにしておいてください。ウィンドウを閉じるとプロキシが停止します。 検索結果の下の展開可能な HTTP ヘッダー セクションに、`X-MSEdge-ClientID` ヘッダー (など) が表示され、各要求で同じであることを確認できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Visual Search モバイル アプリのチュートリアル](computer-vision-web-search-tutorial.md)

> [!div class="nextstepaction"]
> [Bing Web Search API リファレンス](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
