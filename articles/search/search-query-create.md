---
title: 基本的なクエリの作成
titleSuffix: Azure Cognitive Search
description: Cognitive Search でクエリ要求を作成する方法、テストおよびコードに使用するツールと API、およびどのようにしてインデックスのデザインからクエリ決定が開始されるのかについて説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: db36a77d93735b151ad893b7e25ba86f104e7b90
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510466"
---
# <a name="create-a-query-in-azure-cognitive-search"></a>Azure Cognitive Search でクエリを作成する

クエリを初めて作成する場合、この記事では、必要なツールおよび API、クエリの作成に使用されるメソッド、およびインデックスの構造とコンテンツがクエリ結果に与える影響について説明します。 クエリ要求の概要については、[クエリの種類と構成](search-query-overview.md)に関するページをご覧ください。

## <a name="choose-tools-and-apis"></a>ツールと API を選択する

クエリを作成するには、ツールまたは API が必要です。 次の推奨事項はいずれも、テストおよび運用ワークロードに役立ちます。

| 手法 | 説明 |
|-------------|-------------|
| ポータル| [検索エクスプローラー (ポータル)](search-explorer.md) は、Azure portal のクエリ インターフェイスで、基になる検索サービスでインデックスに対してクエリを実行します。 このポータルでは、バックグラウンドで[ドキュメントの検索](/rest/api/searchservice/search-documents)操作に対して REST API 呼び出しを行いますが、オートコンプリート、提案、またはドキュメント検索を呼び出すことはできません。<br/><br/> プレビューを含む、任意のインデックスと REST API バージョンを選択できます。 クエリ文字列では、すべてのクエリ パラメーター (filter、select、searchFields など) をサポートする単純な構文または完全な構文を使用できます。 ポータルでは、インデックスを開くと、フィールド属性に簡単にアクセスできるように、横に並んだタブでインデックス JSON 定義と共に検索エクスプローラーを操作できます。 クエリのテスト中に、検索可能、並べ替え可能、フィルター可能、およびファセット可能の各フィールドを確認します。 <br/>早期調査、テスト、検証用に推奨されます。 [詳細情報。](search-explorer.md) |
| Web テスト ツール| [Postman または Visual Studio Code](search-get-started-rest.md) は、[ドキュメントの検索](/rest/api/searchservice/search-documents)要求、およびその他の要求を REST で作成するための強力な選択肢です。 REST API は、Azure Cognitive Search での考えられるすべてのプログラム操作をサポートしており、Postman や Visual Studio Code などのツールを使用すると、要求を対話的に発行して、コードで使用する前にこの機能の動作を理解できます。 Azure portal で共同作成者または管理者の権限がない場合は、Web テストツールを選択することをお勧めします。 検索 URL とクエリ API キーがある限り、ツールを使用して既存のインデックスに対してクエリを実行できます。 |
| Azure SDK | コードを記述する準備ができたら、.NET、Python、JavaScript、または Java 用の Azure SDK 内の Azure.Search.Document クライアント ライブラリを使用できます。 各 SDK は独自のリリース スケジュールに基づいていますが、すべての SDK でインデックスを作成し、インデックスに対してクエリを実行できます。 <br/><br/>[SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) を使用すると、C# で検索インデックスのクエリを実行できます。  [詳細情報。](search-howto-dotnet-sdk.md)<br/><br/>[SearchClient (Python)](/dotnet/api/azure.search.documents.searchclient) を使用すると、Python で検索インデックスのクエリを実行できます。 [詳細情報。](search-get-started-python.md)<br/><br/>[SearchClient (JavaScript)](/dotnet/api/azure.search.documents.searchclient) を使用すると、JavaScript で検索インデックスのクエリを実行できます。 [詳細情報。](search-get-started-javascript.md) |

## <a name="set-up-a-search-client"></a>検索クライアントを設定する

検索クライアントは、検索サービスに対して認証を行い、要求を送信し、応答を処理します。 使用するツールまたは API に関係なく、検索クライアントには次のプロパティが必要です。

| Properties | 説明 |
|------------|-------------|
| エンドポイント | 検索サービスは、`https://[service-name].search.windows.net` の形式でアドレス指定可能な URL です。 |
| API アクセスキー (管理者またはクエリ) | 検索サービスに対する要求の認証に使用されます。 |
| インデックス名 | クエリは、常に単一のインデックスのドキュメント コレクションを対象とします。 複数のインデックスを結合したり、カスタム データ構造や一時的なデータ構造をクエリの対象にしたりすることはできません。 |
| API バージョン | REST 呼び出しでは、要求に対して `api-version` が明示的に要求されます。 これに対し、Azure SDK 内のクライアント ライブラリは、特定の REST API バージョンに対してバージョン管理されます。 SDK の場合、`api-version` は暗黙的です。 |

### <a name="in-the-portal"></a>ポータルで

Search エクスプローラーとその他のポータル ツールには、サービスへのクライアント接続が組み込まれており、ポータルページからインデックスや他のオブジェクトに直接アクセスできます。 ツール、ウィザード、およびオブジェクトへのアクセスには、サービスに対する共同作成者ロール以上のメンバーシップが必要です。 

### <a name="using-rest"></a>REST の使用

REST 呼び出しの場合は、[Postman または類似のツール](search-get-started-rest.md)をクライアントとして使用して、[ドキュメントの検索](/rest/api/searchservice/search-documents)要求を指定できます。 各要求はスタンドアロンであるため、要求ごとにエンドポイント、インデックス名、および API バージョンを指定する必要があります。 その他のプロパティ、Content-Type、API キーは、要求ヘッダーで渡されます。 

POST または GET を使用して、インデックスのクエリを実行できます。 POST は、パラメーターを要求本文で指定して、簡単に操作できます。 POST を使用する場合は、URL に必ず `docs/search` を含めてください。

```http
POST https://myservice.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

### <a name="using-azure-sdks"></a>Azure SDK の使用

Azure SDK を使用している場合は、コードでクライアントを作成します。 すべての SDK には、状態を保持できる検索クライアントが用意されており、接続を再利用できます。 クエリ操作の場合は **`SearchClient`** をインスタンス化し、エンドポイント、キー、インデックスのプロパティの値を指定します。 その後、 **`Search method`** を呼び出して、クエリ文字列を渡すことができます。 

| 言語 | クライアント | 例 |
|----------|--------|---------|
| C# および .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [C# で最初の検索クエリを送信する](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Python での最初の検索クエリを送信する](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Java で最初の検索クエリを送信する](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [JavaScript で最初の検索クエリを送信する](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>パーサーの選択: simple | full

クエリがフルテキスト検索の場合は、検索パラメーターの内容を処理するためにパーサーが使用されます。 Azure Cognitive Search には、2 つのクエリ パーサーが用意されています。 単純なパーサーは、[単純なクエリ構文](query-simple-syntax.md)を認識します。 このパーサーは、自由形式のテキスト クエリの速度と有効性により既定で選択されています。 この構文では、用語検索と語句検索に共通の検索演算子 (AND、OR、NOT) とプレフィックス (`*`) 検索をサポートしています (Seattle や Seaside に対する "sea*" など)。 一般的には、最初に単純なパーサーを試してから、アプリケーションの要件でより強力なクエリが必要になる場合に完全なパーサーに移行することをお勧めします。

[完全な Lucene クエリ構文](query-Lucene-syntax.md#bkmk_syntax)は、`queryType=full` を要求に追加したときに有効になり、[Apache Lucene パーサー](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)に基づいています。

完全な構文と単純な構文は、両方が同じプレフィックスとブール演算をサポートしているという点で重複しますが、完全な構文のほうがより多くの演算子を用意しています。 完全では、ブール式の演算子や、あいまい検索、ワイルドカード検索、近接検索、正規表現などの高度なクエリ用の演算子が多数用意されています。

## <a name="choose-query-methods"></a>クエリ メソッドを選択する

検索は基本的にユーザー主導の動作であり、検索ボックスから、またはページでのクリック イベントから、用語や語句が収集されます。 次の表は、予想される検索エクスペリエンスと共にユーザー入力を収集するためのメカニズムをまとめたものです。

| 入力 | エクスペリエンス |
|-------|---------|
| [検索メソッド](/rest/api/searchservice/search-documents) | ユーザーは、演算子の有無にかかわらず検索ボックスに用語または語句を入力し、[検索] をクリックして要求を送信します。 検索は同じ要求でフィルターと共に使用できますが、オートコンプリートや提案と共には使用できません。 |
| [オートコンプリート メソッド](/rest/api/searchservice/autocomplete) | ユーザーがいくつかの文字を入力すると、新しい文字が入力されるたびにクエリが開始されます。 応答は、インデックスからの完成した文字列になります。 指定された文字列が有効な場合、ユーザーは [検索] をクリックしてそのクエリをサービスに送信します。 |
| [提案メソッド](/rest/api/searchservice/suggestions) | オートコンプリートと同様に、ユーザーがいくつかの文字を入力すると、増分クエリが生成されます。 応答は一致したドキュメントのドロップダウン リストであり、通常はいくつかの一意または説明のフィールドで表されます。 いずれかの選択が有効な場合、ユーザーが 1 つをクリックすると、一致したドキュメントが返されます。 |
| [ファセット ナビゲーション](/rest/api/searchservice/search-documents#query-parameters) | ページには、クリック可能なナビゲーション リンクまたは階層リンクが表示され、検索範囲を絞り込むことができます。 ファセット ナビゲーション構造は、初期クエリに基づいて動的に構成されます。 たとえば、`search=*` は、可能なすべてのカテゴリから構成されるファセット ナビゲーション ツリーを設定します。 ファセット ナビゲーション構造はクエリ応答から作成されますが、次のクエリを表現するためのメカニズムでもあります。 REST API リファレンスでは、`facets` は、ドキュメントの検索操作のクエリ パラメーターとして記述されていますが、`search` パラメーターを指定せずに使用できます。|
| [フィルター メソッド](/rest/api/searchservice/search-documents#query-parameters) | フィルターは、結果を絞り込むためにファセットと共に使用されます。 また、ページの背後にフィルターを実装することもでき、たとえば、言語固有のフィールドでページを初期化することができます。 REST API リファレンスでは、`$filter` はドキュメントの検索操作のクエリ パラメーターとして記述されていますが、`search` パラメーターを指定せずに使用できます。|

## <a name="know-your-field-attributes"></a>フィールド属性を把握する

以前に[クエリ要求の基本](search-query-overview.md)を確認していた場合は、クエリ要求のパラメーターは、インデックスでフィールドにどのような属性が付けられているかによって異なるということを覚えているかもしれません。 たとえば、クエリ、フィルター、または並べ替え順序で使用するには、フィールドを *検索可能*、*フィルター可能*、および *並べ替え可能* にする必要があります。 同様に、*取得可能* としてマークされたフィールドのみを結果に表示することができます。 要求で `search`、`filter`、および `orderby` パラメーターを指定し始めるときには、予期しない結果を避けるために、必ず属性を確認してください。

[ホテルのサンプル インデックス](search-get-started-portal.md)の下にあるポータルのスクリーンショットでは、`"$orderby"` だけの句で使用できるのは、"LastRenovationDate" と "Rating" の最後の 2 つのフィールドだけです。

![ホテル サンプルのインデックス定義](./media/search-query-overview/hotel-sample-index-definition.png "ホテル サンプルのインデックス定義")

フィールド属性の詳細については、[インデックス (REST API) の作成](/rest/api/searchservice/create-index)に関するページをご覧ください。

## <a name="know-your-tokens"></a>トークンを把握する

インデックス作成中、クエリ エンジンはアナライザーを使用して文字列のテキスト分析を実行し、クエリ時の照合の可能性を最大化します。 少なくとも文字列は小文字に変換されますが、レンマ化とストップ ワードの削除も行われる可能性があります。 通常、長い文字列や複合語は、空白、ハイフン、またはダッシュによって分割され、個別のトークンとしてインデックスが付けられます。 

ここでの要点は、インデックスに含まれていると思われることと、実際に含まれていることは異なる場合があるということです。 クエリで予想された結果が返されない場合は、[分析テキスト (REST API)](/rest/api/searchservice/test-analyzer) を通じてアナライザーによって作成されたトークンを検査できます。 トークン化とクエリへの影響の詳細については、「[部分的な用語検索と特殊文字を含むパターン](search-query-partial-matching.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

これで、クエリ要求の作成方法の理解が深まったので、次のクイックスタートで実際に体験してみてください。

+ [Search エクスプローラー](search-explorer.md)
+ [REST におけるクエリの実行方法](search-get-started-rest.md)
+ [.NET におけるクエリの実行方法](search-get-started-dotnet.md)
+ [Python におけるクエリの実行方法](search-get-started-python.md)
+ [JavaScript におけるクエリの実行方法](search-get-started-javascript.md)