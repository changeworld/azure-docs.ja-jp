---
title: Bing Image Search シングルページ Web アプリ | Microsoft Docs
description: シングルページ Web アプリケーションで Bing Image Search API を使用する方法を説明します。
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: d0e1dc24513c8fc3a405cf1c18f531a0c58fad13
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377029"
---
# <a name="tutorial-single-page-web-app"></a>チュートリアル: シングルページ Web アプリ

Bing Image Search API を使うと、Web を検索して、検索クエリの内容に該当する画像の結果を取得できます。 このチュートリアルでは、Bing Image Search API を使用して検索結果をページ内に表示するシングルページ Web アプリケーションを構築します。 このアプリケーションには、HTML、CSS、JavaScript のコンポーネントが含まれます。

<!-- Remove until we can sanitize images
![[Single-page Bing Image Search app]](media/cognitive-services-bing-images-api/image-search-spa-demo.png)
-->

> [!NOTE]
> ページの下部にある JSON と HTTP のヘッダーをクリックすると、JSON 応答と HTTP 要求の情報が表示されます。 これらの詳細情報は、サービスの詳細を調べるのに役立ちます。

このチュートリアル アプリは、次の方法を示しています。

> [!div class="checklist"]
> * Bing Image Search API 呼び出しを JavaScript で実行する
> * Bing Image Search API に検索オプションを渡す
> * 検索結果を表示する
> * 検索結果をページングする
> * Bing のクライアント ID と API のサブスクリプション キーを処理する
> * 発生する可能性のあるエラーを処理する

このチュートリアルのページでは、すべてが自己完結しています。外部のフレームワーク、スタイル シートはもちろん、画像ファイルすら使用することはありません。 広くサポートされている JavaScript 言語機能のみを使用し、現在のバージョンの主要な Web ブラウザーすべてで動作します。

このチュートリアルでは、ソース コードの一部についてのみ説明します。 完全なソース コードは[別のページ](tutorial-bing-image-search-single-page-app-source.md)から入手できます。 このコードをコピーしてテキスト エディターに貼り付け、`bing.html` として保存してください。

> [!NOTE]
> このチュートリアルは、[シングルページ Bing Web Search アプリのチュートリアル](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md)とほぼ同じものですが、画像検索結果のみを扱っています。

## <a name="app-components"></a>アプリのコンポーネント

他のシングルページ Web アプリと同様に、このチュートリアル用アプリケーションには 3 つのパーツが含まれています。

> [!div class="checklist"]
> * HTML - ページの構造と内容を定義します
> * CSS - ページの外観を定義します
> * JavaScript - ページの動作を定義します

このチュートリアルでは、HTML と CSS の大部分について詳しく取り上げません。HTML と CSS はわかりやすいためです。

HTML には検索フォームが含まれ、ユーザーはこれにクエリを入力して検索オプションを選択します。 フォームは JavaScript に接続されます。JavaScript は、`<form>` タグの `onsubmit` 属性を使用して、実際に検索を実行します。

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
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

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

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

HTML の `<form>` タグの `onsubmit` は、検索結果を返す `bingWebSearch` 関数を呼び出します。 `bingWebSearch` は `getSubscriptionKey` を使用して各クエリを認証します。 前の定義のとおり、キーが入力されていない場合は、`getSubscriptionKey` はユーザーにキーの入力を求めます。 その後、アプリケーションで引き続き使用できるよう、キーが格納されます。

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>検索オプションの選択

![[Bing Image Search のフォーム]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

この HTML のフォームには、次のコントロールが含まれます。

| | |
|-|-|
|`where`|検索で使用される市場 (場所と言語) を選択するドロップダウン メニュー。|
|`query`|検索語句を入力するテキスト フィールド。|
|`aspect`|検索結果の画像の形を選択するラジオ ボタン。大まかに分類すると、正方形、ワイド、縦長を選択できます。|
|`color`|カラー、白黒、または主色を選択します。
|`when`|オプションとして、最近の日、週、または月に検索を限定するためのドロップダウン メニュー。|
|`safe`|"成人向け" の結果をフィルターで除外する Bing のセーフサーチ機能を使用するかどうかを指定するチェック ボックス。|
|`count`|隠しフィールド。 各要求に対して返される検索結果の数。 変更すると、1 ページあたりの結果の表示数が増減します。|
|`offset`|隠しフィールド。 要求における最初の検索結果のオフセット。ページングに使用されます。 新しい要求では `0` にリセットされます。|
|`nextoffset`|隠しフィールド。 検索結果を受け取ると、このフィールドは応答内で `nextOffset` という値に設定されます。 このフィールドを使用すると、連続するページで結果が重複することを回避できます。|
|`stack`|隠しフィールド。 JSON でエンコードされた、検索結果の前のページのオフセットの一覧。前のページに戻るために使用します。|

> [!NOTE]
> Bing Image Search には、もっと多くのクエリ パラメーターがあります。 ここではその一部のみを使用しています。

JavaScript 関数 `bingSearchOptions()` は、これらのフィールドを、Bing Search API で必要とされる形式の部分的なクエリ文字列に変換します。

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

たとえば、セーフサーチ機能は、`strict`、`moderate`、`off` のいずれかにできます。既定値は `moderate` です。 ただし、このフォームでは、2 つの状態しかないチェック ボックスを使用します。 JavaScript コードにより、この設定を `strict` または `off` のいずれかに変換します (`moderate` は使用しません)。

## <a name="performing-the-request"></a>要求の実行

クエリ、オプションの文字列、および API キーを指定すると、`BingImageSearch` 関数は `XMLHttpRequest` オブジェクトを使用して Bing Image Search エンドポイントに対して要求を行います。

```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

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
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
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
> HTTP 要求が成功しても、必ずしも検索自体が成功したとは "*限りません*"。 検索操作でエラーが発生した場合、Bing Image Search API は 200 以外の HTTP 状態コードを返し、JSON 応答にエラー情報を含めます。 また、要求のレートが制限されている場合、API は空の応答を返します。

先ほどの両方の関数内にあるコードの多くは、エラー処理専用のものです。 エラーは次のステージで発生する場合があります。

|段階|起こり得るエラー|処理元|
|-|-|-|
|JavaScript 要求オブジェクトのビルド|無効な URL|`try`/`catch` ブロック|
|要求の実行|ネットワーク エラー、接続の中止|`error` および `abort` イベント ハンドラー|
|検索の実行|無効な要求、無効な JSON、レート制限|`load` イベント ハンドラー内のテスト|

エラーは、`renderErrorMessage()` とエラーに関する既知の情報を呼び出すことで処理されます。 応答がエラー テストにすべてパスしたら、`renderSearchResults()` を呼び出して、ページ内に検索結果を表示します。

## <a name="displaying-search-results"></a>検索結果の表示

検索結果を表示するための主な関数は `renderSearchResults()` です。 この関数は、Bing Image Search サービスによって返される JSON を受け取り、画像または関連する検索がある場合はそれらをレンダリングします。

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

主な画像検索結果は、JSON 応答で最上位の `value` オブジェクトとして返されます。 `renderImageResults()` 関数は、これを受け取って反復処理した後、別の関数を呼び出して各項目を HTML としてレンダリングします。 結果の HTML は、`renderSearchResults()` に返されてから、ページ内の `results` ブロックに挿入されます。

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

Bing Image Search API は、関連する結果を最大で 4 種類返します。これらの各結果は、それ自体の最上位のオブジェクトに含まれます。 次に例を示します。

|||
|-|-|
|`pivotSuggestions`|元の検索の軸語を別のものに置き換えるクエリです。 たとえば、"赤い花" を検索した場合、"赤" が軸語で、"黄色い花" が候補になる可能性があります。|
|`queryExpansions`|複数の条件を追加することで、元の検索を絞り込むクエリです。 たとえば、"Microsoft Surface" を検索した場合、クエリが "Microsoft Surface Pro" に拡張される場合があります。|
|`relatedSearches`|元の検索を入力した他のユーザーが他に入力したことのあるクエリです。 たとえば、"レーニア山" を検索した場合、関連する検索は "セント  ヘレンズ山" となる場合があります。|
|`similarTerms`|元の検索と似た意味を持つクエリです。 たとえば、"子猫" を検索すると、似た用語として "かわいい" が挙げられる場合があります。|

`renderSearchResults()` で確認したように、`relatedItems` の候補だけがレンダリングされ、結果のリンクはページのサイドバーに配置されます。

## <a name="rendering-result-items"></a>結果項目のレンダリング

JavaScript コードにはオブジェクト `searchItemRenderers` が存在します。これには *renderers:* 関数が含まれ、各種類の検索結果ごとに HTML を生成します。

```javascript
searchItemRenderers = { 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

レンダラー関数では、次のパラメーターを受け取ることができます。

| | |
|-|-|
|`item`|項目のプロパティ (URL とその説明など) を含む JavaScript オブジェクト。|
|`index`|コレクション内の結果項目のインデックス。|
|`count`|検索結果項目のコレクション内の項目の数。|

`index` パラメーターと `count` パラメーターは、結果のカウント、コレクションの先頭または末尾の特別な HTML の生成、特定の数の項目の後への改行の挿入などに使用できます。 レンダラーにこの機能が必要ない場合は、これら 2 つのパラメーターを受け取る必要はありません。

`images` レンダラーについて詳しく見ていきましょう。

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

この画像レンダラー関数は次の処理を行います。

> [!div class="checklist"]
> * 画像のサムネイルのサイズを計算します (幅は 120 ピクセル以上の可変ですが、高さは 90 ピクセルに固定されます)。
> * 画像のサムネイルを表示する HTML `<img>` タグをビルドします。 
> * 画像およびその画像が含まれるページにリンクされる HTML `<a>` タグをビルドします。
> * 画像およびその画像が存在するサイトに関する情報を示す説明をビルドします。

最初の画像の結果の前に `<p>` タグを挿入するために、`index` 変数をテストします。 そうしないと、サムネイルが互いに干渉し合い、ブラウザー ウィンドウ内で必要に応じてラップしてしまいます。

サムネイルのサイズは、サムネイルの URL 内の `<img>` タグに加えて、`h` および `w` フィールドで使用されます。 その後、[Bing サムネイル サービス](resize-and-crop-thumbnails.md)から、そのサイズのとおりのサムネイルが提供されます。

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
> [Bing Image Search API リファレンス](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

