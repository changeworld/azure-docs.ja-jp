---
title: suggester の作成
titleSuffix: Azure Cognitive Search
description: suggester を作成し、オートコンプリートまたは自動候補クエリの用語を呼び出す要求を作成して、Azure Cognitive Search における先行入力クエリのアクションを有効にします。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: a03ca4bcad9bb577db68e2728ff9dbebb5779a7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626828"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>クエリでオートコンプリートと候補の結果を有効にする suggester を作成する

Azure Cognitive Search では、*suggester* によって先行入力、または "逐次検索" が有効になります。 suggester により、追加のトークン化が行われ、部分的な用語での一致をサポートするプレフィックス シーケンスが生成されるフィールドの一覧が提供されます。 たとえば、"Seattle" の値を持つ City フィールドを含む suggester のプレフィックスの組み合わせは、先行入力をサポートする "sea"、"seat"、"seatt"、"seattl" になります。

部分的用語での一致は、オートコンプリート クエリと一致候補のいずれかです。 同じ suggester は、両方のエクスペリエンスをサポートします。

## <a name="typeahead-experiences-in-cognitive-search"></a>Cognitive Search の先行入力エクスペリエンス

先行入力は、用語全体のクエリの一部入力を補完する "*オートコンプリート*" と、特定の一致へのクリック スルーを促す "*検索候補*" です。 オートコンプリートでは、クエリが生成されます。 候補では、一致するドキュメントが生成されます。

次のスクリーンショットは、[C# で初めてアプリを作成する](tutorial-csharp-type-ahead-and-suggestions.md)方法に関するページのものですが、その両方を示しています。 オートコンプリートを使用すると、可能性のある語句が予測され、"tw" に "in" が補完されます。 候補はミニ検索結果であり、ホテル名のようなフィールドには、インデックスから一致するホテルの検索ドキュメントが表示されます。 候補については、説明的な情報を提供する任意のフィールドを表示できます。

![オートコンプリートされたクエリと提案されたクエリの視覚的な比較](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "オートコンプリートされたクエリと提案されたクエリの視覚的な比較")

これらの機能は、個別に、または組み合わせて使用できます。 Azure Cognitive Search でこれらの動作を実装するために、インデックスとクエリのコンポーネントがあります。 

+ 検索インデックスの定義に suggester を追加します。 この記事の残りの部分では、suggester の作成に焦点を絞って説明します。

+ [以下に一覧されているいずれかの API](#how-to-use-a-suggester) を使用して、候補要求またはオートコンプリート要求の形式で suggester 対応クエリを呼び出します。

文字列フィールドの場合、フィールドごとに逐次検索のサポートが有効になります。 スクリーンショットに示したものと同様のエクスペリエンスを求めているのであれば、同じ検索ソリューション内に両方の先行入力の動作を実装できます。 どちらの要求でも特定のインデックスの *documents* コレクションをターゲットとし、ユーザーが入力文字列を 3 文字以上指定した時点から応答が返されます。

## <a name="how-to-create-a-suggester"></a>suggester を作成する方法

suggester を作成するには、それを[インデックス定義](/rest/api/searchservice/create-index)に追加します。 suggester によって、先行入力エクスペリエンスが有効になっているフィールドの名前とコレクションが取得されます。 suggester の作成に最適なタイミングは、それを使用するフィールドを定義するときです。

+ 文字列フィールドのみを使用します。

+ 文字列フィールドが複合型 (Address 内にある City フィールドなど) の一部である場合は、フィールド パスに親を含めます。`"Address/City"` (REST、および C# と Python)、または `["Address"]["City"]` (JavaScript)。

+ フィールドで、既定の標準 Lucene アナライザー (`"analyzer": null`) または [言語アナライザー](index-add-language-analyzers.md) (`"analyzer": "en.Microsoft"` など) を使用します。

既存のフィールドを使用して suggester を作成しようとすると、API ではそれは許可されません。 プレフィックスは、インデックス作成の間に、2 つ以上の文字の組み合わせから成る部分的な用語が完全な用語と並行してトークン化されるときに生成されます。 既存のフィールドが既にトークン化されている場合、それらを suggester に追加するには、インデックスを再構築する必要があります。 詳細については、[Azure Cognitive Search インデックスを再構築する方法](search-howto-reindex.md)に関するページを参照してください。

### <a name="choose-fields"></a>フィールドの選択

suggester は、いくつかのプロパティを備えていますが、基本的には、search-as-you-type エクスペリエンスを有効にしている文字列フィールドのコレクションです。 インデックスごとに 1 つの suggester があるため、suggester リストには、候補とオートコンプリートの両方のコンテンツを提供するすべてのフィールドを含める必要があります。

追加のコンテンツには、より多くの用語が補完候補として含まれるため、オートコンプリートには、大きいフィールド プールの方がより有効です。

一方、候補では、フィールド選択が選択的である場合により良い結果が得られます。 候補は検索ドキュメントのプロキシであるため、単一の結果を最もよく表すフィールドが必要になることに注意してください。 複数の一致を区別する名前、タイトル、またはその他の固有のフィールドが最適です。 フィールドが反復される値で構成されている場合、候補は同じ結果で構成され、ユーザーはどれをクリックすればよいかがわかりません。

両方の逐次検索エクスペリエンスを満たすには、オートコンプリートに必要なすべてのフィールドを追加しますが、その後、"$select"、"$top"、"$filter"、"searchFields" を使用して、検索候補の結果を制御します。

### <a name="choose-analyzers"></a>アナライザーの選択

アナライザーを選択すると、フィールドをトークン化する方法と、その後のプレフィックスを付ける方法が決まります。 たとえば、"context-sensitive" などのハイフンでつながれた文字列の場合、言語アナライザーを使用すると、"context"、"sensitive"、"context-sensitive" というトークンの組み合わせになります。 ここでは標準の Lucene アナライザーを使用したので、ハイフンでつながれた文字列は存在しません。 

アナライザーを評価するときは、[テキスト分析 API](/rest/api/searchservice/test-analyzer) を使用して、用語の処理方法を把握することを検討してください。 インデックスを作成したら、文字列に対してさまざまなアナライザーを試して、トークン出力を確認することができます。

[カスタム アナライザー](index-add-custom-analyzers.md)または[組み込みアナライザー](index-add-custom-analyzers.md#built-in-analyzers) (標準の Lucene を除く) を使用するフィールドは、不適切な結果を防ぐために明示的に許可されません。

> [!NOTE]
> アナライザーの制約を回避する必要がある場合 (たとえば、特定のクエリ シナリオのためにキーワードまたは ngram アナライザーが必要な場合)、同じコンテンツに対して 2 つの別個のフィールドを使用する必要があります。 これにより、一方のフィールドで suggester を指定し、他方をカスタム アナライザーの構成で設定することができます。

## <a name="create-using-the-portal"></a>ポータルを使用して作成する

**インデックスの追加** または **データ インポート** ウィザードを使用してインデックスを作成する場合は、suggester を有効にするオプションがあります。

1. インデックスの定義で、suggester の名前を入力します。

1. 新しいフィールドの各フィールド定義で、[Suggester] 列のチェックボックスをオンにします。 チェックボックスは、文字列フィールドでのみ使用できます。 

前述のように、アナライザーの選択はトークン化とプレフィックス付けに影響します。 suggester を有効にする場合は、フィールド定義全体を考慮してください。 

## <a name="create-using-rest"></a>REST を使用して作成する

REST API では、[インデックス作成](/rest/api/searchservice/create-index)または[インデックス更新](/rest/api/searchservice/update-index)を使用して suggester を追加します。 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>.NET を使用して作成する

C# で、[SearchSuggester オブジェクト](/dotnet/api/azure.search.documents.indexes.models.searchsuggester)を定義します。 `Suggesters` は SearchIndex オブジェクトのコレクションですが、1 つのアイテムのみを受け取ることができます。 インデックスの定義に suggester を追加します。

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
    definition.Suggesters.Add(suggester);

    indexClient.CreateOrUpdateIndex(definition);
}
```

## <a name="property-reference"></a>プロパティ リファレンス

|プロパティ      |説明      |
|--------------|-----------------|
| name        | suggester の定義に指定しますが、オートコンプリートまたは候補の要求でも呼び出されます。 |
| sourceFields | suggester 定義で指定します。 候補の内容のソースであるインデックスの 1 つまたは複数のフィールドのリストです。 フィールドは `Edm.String` 型および `Collection(Edm.String)` 型である必要があります。 フィールドにアナライザーが指定されている場合は、(カスタム アナライザーではなく) [こちらの一覧](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername)の名前付き語彙アナライザーである必要があります。 </br></br>ベスト プラクティスとしては、検索バーまたはドロップダウン リストのどちらの補完された文字列でも、想定される適切な応答に利用するフィールドのみを指定します。 </br></br>ホテル名には有効桁数があるため、適切な選択肢です。 説明やコメントなどの詳細なフィールドは、文字数が多くなりすぎます。 同様に、カテゴリやタグなどの反復的なフィールドでは、効果が低くなります。 例には、複数のフィールドを含めることができることを例示するために、ひとまず、"カテゴリ" を組み入れています。 |
| searchMode  | REST のみのパラメーターですが、ポータルにも表示されます。 このパラメーターは .NET SDK では使用できません。 候補語句の検索に使用される戦略を示します。 現在サポートされているモードは `analyzingInfixMatching` のみです。これは、現在、用語の先頭でマッチングを実行します。|

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>suggester を使用する

クエリには suggester が使用されています。 suggester を作成したら、search-as-you-type エクスペリエンス用の次の API のいずれかを呼び出します。

+ [検索候補 REST API](/rest/api/searchservice/suggestions)
+ [オートコンプリート REST API](/rest/api/searchservice/autocomplete)
+ [SuggestAsync メソッド](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [AutocompleteAsync メソッド](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

検索アプリケーションでは、クライアント コードで、[jQuery UI Autocomplete](https://jqueryui.com/autocomplete/) などのライブラリを利用して、部分クエリを収集し、一致を提供する必要があります。 このタスクの詳細については、[クライアント コードへのオートコンプリートまたは候補の結果の追加](search-add-autocomplete-suggestions.md)に関するページを参照してください。

API の使用方法については、オートコンプリート REST API の次の呼び出しで説明します。 この例では、2 つのポイントがあります。 第一に、すべてのクエリと同様に、操作はインデックスの documents コレクションに対して行われ、クエリには "search" パラメーターが含まれます。この場合、部分クエリが提供されます。 第二に、"suggesterName" を要求に追加する必要があります。 インデックスで suggester が定義されていない場合、オートコンプリートまたは検索候補の呼び出しは失敗します。

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>サンプル コード

+ [Web サイトへの検索の追加 (JavaScript)](tutorial-javascript-search-query-integration.md#azure-function-suggestions-from-the-catalog) では、部分的な用語補完のためのオープンソースの検索候補パッケージをクライアント アプリで使用します。

+ [C# での最初のアプリの作成 (レッスン 3 - 逐次検索の追加)](tutorial-csharp-type-ahead-and-suggestions.md)サンプルでは、提案されたクエリ、オートコンプリート、ファセット ナビゲーションが示されています。 このコードにより、ウィジェットを使用する代わりに、先行入力のネイティブ サポートが提供されます。

## <a name="next-steps"></a>次のステップ

要求と定式化の詳細について学習します。

> [!div class="nextstepaction"]
> [クライアント コードにオートコンプリートと検索候補を追加する](search-add-autocomplete-suggestions.md)