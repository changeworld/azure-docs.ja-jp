---
title: Azure Search を使用して検索ボックスにオートコンプリートを追加するチュートリアル | Microsoft Docs
description: データ中心アプリケーションのエンド ユーザー エクスペリエンスを Azure Search のオートコンプリート API と検索候補 API を使用して向上させる方法の例。
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: mcarter
ms.openlocfilehash: 7120080bfdc188c150c7065e1c0639ab8c04f173
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989686"
---
# <a name="tutorial-add-auto-complete-to-your-search-box-using-azure-search"></a>チュートリアル: Azure Search を使用して検索ボックスにオートコンプリートを追加する

このチュートリアルでは、[Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/) と [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions?view=azure-dotnet) の[検索候補](https://docs.microsoft.com/rest/api/searchservice/suggestions)、[オートコンプリート](https://docs.microsoft.com/en-us/rest/api/searchservice/autocomplete)、および[ファセット](search-faceted-navigation.md)を使用して、パワフルな検索ボックスを作成します。 "*検索候補*" は、その時点までにユーザーが入力した内容に基づく実際の検索結果を候補として提示します。 Azure Search の[新しいプレビュー機能](search-api-preview.md)である "*オートコンプリート*" は、インデックスから取得した用語を提示して、ユーザーが現在入力している内容を補完します。 ここでは、ユーザーの入力時に豊富な方法で検索できるようにすることで、ユーザーの生産性を向上させ、ユーザーが探しているものをすばやく簡単に見つけるための複数のテクニックを比較していきます。

このチュートリアルでは、C# を使用して [Azure Search .NET クライアント ライブラリ](https://aka.ms/search-sdk) を呼び出し、JavaScript を使用して Azure Search REST API を直接呼び出す ASP.NET MVC ベースのアプリケーションについて説明します。 このチュートリアルのアプリケーションは、[NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) サンプル インデックスを対象にしています。 NYCJobs デモに既に構成されているインデックスを使用するか、NYCJobs サンプル ソリューションのデータ ローダーを使用して独自のインデックスにデータを入力できます。 このサンプルでは、JavaScript ライブラリの [jQuery UI](https://jqueryui.com/autocomplete/) と [XDSoft](https://xdsoft.net/jqplugins/autocomplete/)を使用して、オートコンプリートをサポートする検索ボックスを作成します。 これらのコンポーネントを Azure Search と共に使用して、検索ボックスでの先行入力によるオートコンプリートをサポートする方法の複数の例を確認します。

以下のタスクを実行します。

> [!div class="checklist"]
> * ソリューションをダウンロードして構成する
> * Search サービスの情報をアプリケーション設定に追加する
> * 検索入力ボックスを実装する
> * リモート ソースから取得するオートコンプリートの一覧のサポートを追加する 
> * .Net SDK と REST API を使用して検索候補とオートコンプリートを取得する
> * パフォーマンス向上のためにクライアント側のキャッシュをサポートする 

## <a name="prerequisites"></a>前提条件

* Visual Studio 2017。 無料の [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) を使用できます。 

* このチュートリアル用のサンプル [ソース コード](https://github.com/azure-samples/search-dotnet-getting-started)をダウンロードします。

* (省略可能) アクティブな Azure アカウントと Azure Search サービス。 Azure アカウントを持っていない場合、[無料試用版](https://azure.microsoft.com/free/)でサインアップできます。 サービスのプロビジョニングについては、[Search サービスの作成](search-create-service-portal.md)に関する記事を参照してください。 このチュートリアルは、別のデモのために既に用意されている、ホストされている NYCJobs インデックスを使用して完了できるため、アカウントとサービスは必須ではありません。

* (省略可能) [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) サンプル コードをダウンロードして、NYCJobs データを Azure Search サービスのインデックスにインポートします。

> [!Note]
> 無料の Azure Search サービスを使用している場合、インデックスは 3 つに制限されています。 NYCJobs データ ローダーは、2 つのインデックスを作成します。 ご利用のサービスに、新しいインデックスを受け入れる余地があることを確認してください。

### <a name="set-up-azure-search-optional"></a>Azure Search を設定する (省略可能)

NYCJobs サンプル アプリケーションのデータを独自のインデックスにインポートする場合は、このセクションの手順に従います。 この手順は省略可能です。  用意されているサンプル インデックスを使用する場合は、次のセクションに進んでサンプルを実行してください。

1. NYCJobs サンプル コードの DataLoader フォルダー内の DataLoader.sln ソリューションを Visual Studio で開きます。

1. Azure Search サービスの接続情報を更新します。  DataLoader プロジェクト内の App.config を開き、TargetSearchServiceName と TargetSearchServiceApiKey appSettings を、利用している Azure Search サービスと Azure Search Service API キーを反映するように更新します。  これらは Azure Portal で確認できます。

1. F5 キーを押してアプリケーションを起動します。  これにより、2 つのインデックスが作成され、NYCJob サンプル データがインポートされます。

1. このチュートリアルのサンプル コードの AutocompleteTutorial.sln ソリューション ファイルを Visual Studio で開きます。  AutocompleteTutorial プロジェクト内の Web.config を開き、SearchServiceName と SearchServiceApiKey の値を上記と同じように変更します。

### <a name="running-the-sample"></a>サンプルの実行

これで、チュートリアルのサンプル アプリケーションを実行する準備が整いました。  チュートリアルを実行するには、AutocompleteTutorial.sln ソリューション ファイルを Visual Studio で開きます。  このソリューションには、ASP.NET MVC プロジェクトが含まれています。  F5 キーを押してプロジェクトを実行し、任意のブラウザーにページを読み込みます。  上部に、C# または JavaScript を選択するためのオプションが表示されます。  C# オプションは、ブラウザーから HomeController を呼び出し、Azure Search .Net SDK を使用して結果を取得します。  JavaScript オプションは、ブラウザーから直接 Azure Search REST API を呼び出します。  このオプションでは、コントローラーがフローから除外されるため、通常はパフォーマンスが大幅に向上します。  自分のニーズと言語設定に合ったオプションを選択できます。  オートコンプリートの例を示すさまざまなページがあり、それぞれにガイダンスが示されています。  それぞれの例に推奨するサンプル テキストがあり、そのテキストを使って試してみることができます。  検索ボックスに数文字を入力して、何が起こるかを観察してください。

## <a name="how-this-works-in-code"></a>コードでの動作のしくみ

ブラウザーで例を確認したので、次は、最初の例を使用して、関連するコンポーネントとそれらがどのように機能しているかを詳しく見ていきましょう。

### <a name="search-box"></a>検索ボックス

どの言語を選択した場合でも、検索ボックスはまったく同じです。  \Views\Home フォルダー内の Index.cshtml ファイルを開きます。 検索ボックス自体は単純です。

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

これは、スタイリング用のクラス、JavaScript によって参照される ID、およびプレースホルダー テキストがある単純な入力テキスト ボックスです。  魔法は Javascript にあります。

### <a name="javascript-code-c"></a>JavaScript コード (C#)

C# 言語のサンプルでは、Index.cshtml 内の JavaScript を使用して、jQuery UI Autocomplete ライブラリを利用します。  このライブラリは、MVC コントローラーに対する非同期呼び出しを実行して検索候補を取得することで、検索ボックスにオートコンプリート エクスペリエンスを追加します。  最初の例の JavaScript コードを見てみましょう。

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

このコードが、ブラウザーでのページの読み込み時に実行され、"example1a" 入力ボックスに対するオートコンプリートが構成されます。  `minLength: 3` は、検索ボックスに少なくとも 3 文字が入力されたときのみ、検索候補を表示することを保証します。  重要なのは、source 値です。

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

この行は、検索ボックスの下に表示する項目の一覧をどこから取得するかをオートコンプリート API に通知します。  これは MVC プロジェクトであるため、それは HomeController.cs 内の Suggest 関数を呼び出します。  これについては、次のセクションで説明します。  強調表示、あいまい一致、および用語を制御するいくつかのパラメーターも渡します。  オートコンプリート JavaScript API は、term パラメーターを追加します。

#### <a name="extending-the-sample-to-support-fuzzy-matching"></a>あいまい一致をサポートするようにサンプルを拡張する

あいまい検索では、ユーザーが検索ボックスに単語を間違って入力した場合でも、類似する一致に基づいて結果を取得できます。  あいまい一致を実行できるように source 行を変更しましょう。

変更元の行は次のとおりです。

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

これを、次のように変更します。

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

F5 キーを押してアプリケーションを起動します。

"execative" と入力してみます。入力した文字と完全に一致していないにもかかわらず、"executive" という結果が返ってくることに注目してください。

### <a name="homecontrollercs-c"></a>HomeController.cs (C#)

サンプルの JavaScript コードの見直しが終わったので、次は、Azure Search .NET SDK を使用して検索候補を実際に取得する C# コントローラーのコードを見ていきましょう。

1. Controllers ディレクトリ内の HomeController.cs ファイルを開きます。 

1. 最初に気付くのは、クラスの上部にある InitSearch と呼ばれるメソッドです。  これは、Azure Search サービスに対する認証済み HTTP インデックス クライアントを作成します。  この動作の詳細については、「[.NET アプリケーションから Azure Search を使用する方法](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)」を参照してください。

1. Suggest 関数に移動します。

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Suggest 関数は、ヒットの強調表示が返されるかどうか、および入力された検索用語に加えてあいまい一致も使用するかどうかを決定する 2 つのパラメーターを使用します。  このメソッドは、検索候補 API に渡される SuggestParameters オブジェクトを作成します。 結果は、クライアントに表示できるように JSON に変換されます。
(省略可能) Suggest 関数の始めにブレークポイントを追加し、コードをステップ実行します。  SDK によって返される応答と、メソッドから返される結果に変換される様子に注目します。

ページ上のその他の例も、同じパターンに従って、ヒットの強調表示、オートコンプリートの先行入力、およびオートコンプリートの結果のクライアント側のキャッシュをサポートするファセットを追加しています。  これらを見直してそのしくみを理解し、検索エクスペリエンスでどのように活用するかを判断してください。

### <a name="javascript-language-example"></a>JavaScript 言語の例

JavaScript 言語の例では、IndexJavaScript.cshtml ページ内の JavaScript コードで、jQuery UI Autocomplete を利用します。  これは、見栄えの良い検索ボックスを提示するための重労働の大半を実行するライブラリであり、検索候補を取得するための Azure Search への非同期呼び出しを簡単に実行できるようにします。  最初の例の JavaScript コードを見てみましょう。

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

これを上記の home コント ローラーを呼び出す例と比較した場合、いくつかの類似点に気付くでしょう。  `minLength` と `position` のオートコンプリート構成は、完全に同じです。  大きな違いは source にあります。  home コントローラー内の Suggest メソッドを呼び出す代わりに、REST 要求が JavaScript 関数内に作成され、ajax を使用して実行されます。  応答は "success" で処理され、source として使用します。

## <a name="takeaways"></a>重要なポイント

このチュートリアルでは、オートコンプリートと検索候補をサポートする検索ボックスを作成するための基本的な手順について説明しています。  ASP.NET MVC アプリケーションをビルドし、Azure Search .Net SDK または REST API を使用して検索候補を取得する方法を確認しました。

## <a name="next-steps"></a>次の手順

検索候補とオートコンプリートを検索エクスペリエンスに統合します。  ユーザーの入力時に Azure Search のパワーを利用できるようにすることでユーザーの生産性を向上させるために、.Net SDK または REST API の直接的な使用をどのように実行するかを考慮します。

> [!div class="nextstepaction"]
> [オートコンプリート REST API](https://docs.microsoft.com/en-us/rest/api/searchservice/autocomplete)
> [検索候補 REST API](https://docs.microsoft.com/en-us/rest/api/searchservice/suggestions)
> [インデックス作成 REST API のファセット インデックス属性](https://docs.microsoft.com/en-us/rest/api/searchservice/create-index)

