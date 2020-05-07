---
title: 部分的な語句、パターン、特殊文字
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search クエリ要求で、ワイルドカード、正規表現、プレフィックスのクエリを使用し、完全な用語または用語の一部を照合します。 一致させるのが難しい、特殊文字を含むパターンを完全に一致させるには、完全なクエリ構文とカスタム アナライザーを使用して解決できます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 05ff56c904fc48a1041ad40f00110a8ff0fd01f1
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592045"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>部分的な用語検索と特殊文字を含むパターン (ワイルドカード、正規表現、パターン)

"*部分的な用語検索*" とは、用語全体ではなく、用語の先頭、中間、または末尾 (プレフィックス、インフィックス、またはサフィックス クエリとも呼ばれます) のみを含む用語のフラグメントで構成されるクエリを指します。 "*パターン*" は、フラグメントを組み合わせて使用することもできます。多くの場合、ダッシュやスラッシュなどの特殊文字をクエリ文字列の一部として使います。 一般的なユース ケースには、電話番号、URL、人間または製品コードの一部、または複合語のクエリなどがあります。

予期される形式の用語がインデックスにない場合、部分検索とパターン検索は問題になる可能性があります。 インデックス作成の[構文分析フェーズ](search-lucene-query-architecture.md#stage-2-lexical-analysis) (既定の標準アナライザーを想定) では、特殊文字は破棄され、複合文字列は分割され、空白は削除されます。このいずれも、一致が見つからない場合にパターン クエリが失敗する原因となる可能性があります。 たとえば、`+1 (425) 703-6214` (`"1"`、`"425"`、`"703"`、`"6214"` としてトークン化) のような電話番号は、実際にはインデックスに存在しないため、`"3-62"` クエリに表示されません。 

解決策は、必要に応じて空白や特殊文字を含む完全な文字列を保持するアナライザーを呼び出し、部分的な用語とパターンで照合できるようにすることです。 そのままの文字列に対して追加のフィールドを作成し、コンテンツを保持するアナライザーを使用することは、この解決策の基盤となります。

> [!TIP]
> Postman と REST API を使い慣れている場合は、 [クエリ例のコレクションをダウンロード](https://github.com/Azure-Samples/azure-search-postman-samples/)して、この記事で説明されている部分的な用語と特殊文字のクエリを実行します。

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Azure Cognitive Search での部分的検索

Azure Cognitive Search では、部分的な検索とパターンを以下のような形式で利用できます。

+ [プレフィックス検索](query-simple-syntax.md#prefix-search) (`search=cap*` など) では、「Cap'n Jack's Waterfront Inn」または「Gacc Capital」などを一致させます。 プレフィックス検索には、単純なクエリ構文または完全な Lucene クエリ構文を使用できます。

+ [ワイルドカード検索](query-lucene-syntax.md#bkmk_wildcard)または[正規表現](query-lucene-syntax.md#bkmk_regex)では、埋め込み文字列のパターンや部分を検索します。 ワイルドカードと正規表現では、完全な Lucene 構文が必要です。 サフィックスとインデックスのクエリは、正規表現として表現されます。

  部分的な用語検索の例には、次のようなものがあります。 サフィックス クエリでは、「英数字」のような用語でワイルドカード検索 (`search=/.*numeric.*/`) を使用して一致を見つけます。 URL フラグメントなどの内部文字を含む部分的な用語では、必要に応じてエスケープ文字を追加します。 JSON では、スラッシュ `/` はバックスラッシュ `\` によってエスケープされます。 このため、`search=/.*microsoft.com\/azure\/.*/` は URL フラグメント「microsoft.com/azure/」の構文です。

前述のように、上記すべての場合、インデックスには、標準アナライザーによって提供されないパターン マッチングに適した形式の文字列が含まれている必要があります。 この記事の手順に従うことにより、これらのシナリオをサポートするために必要なコンテンツが存在することを確認できます。

## <a name="solving-partialpattern-search-problems"></a>部分的検索またはパターン検索の問題の解決

フラグメントまたはパターンまたは特殊文字を検索する必要がある場合は、単純なトークン化ルールで動作するカスタム アナライザーを使用して、既定のアナライザーをオーバーライドできます。これにより、文字列全体が保持されます。 少し前に戻ると、手順は次のようになります。

+ 文字列の完全なバージョンを格納するためのフィールドを定義する (分析されたテキストと分析されていないテキストが必要なものと想定)
+ 定義済みのアナライザーを選択するか、分析されていない完全な文字列を出力するためのカスタム アナライザーを定義する
+ カスタム アナライザーをフィールドに割り当てる
+ インデックスを構築してテストする

> [!TIP]
> アナライザーの評価は、インデックスの再構築を頻繁に行う必要がある反復的なプロセスです。 [インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index)、[インデックスの削除](https://docs.microsoft.com/rest/api/searchservice/delete-index)、[ドキュメントの読み込み](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)、および[ドキュメントの検索](https://docs.microsoft.com/rest/api/searchservice/search-documents)の REST API である Postman を使用することで、これらの手順を簡単に行うことができます。 ドキュメントの読み込みの場合、要求本文には、テストする代表的な小さいデータセットが含まれている必要があります (たとえば、電話番号や製品コードを含むフィールドなど)。 同じ Postman コレクション内のこれらの API を使用すると、これらの手順を迅速に実行できます。

## <a name="duplicate-fields-for-different-scenarios"></a>さまざまなシナリオでフィールドを複製する

アナライザーはフィールドごとに割り当てられます。つまり、インデックスでフィールドを作成し、さまざまなシナリオに合わせて最適化できます。 具体的には、最初のフルテキスト検索をサポートするために "featureCode" と "featureCodeRegex" を定義し、2 番目に高度なパターン マッチングを適用することができます。

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

## <a name="choose-an-analyzer"></a>アナライザーを選択する

用語全体のトークンを生成するアナライザーを選択する場合は、次のアナライザーが一般的な選択肢となります。

| アナライザー | 動作 |
|----------|-----------|
| [言語アナライザー](index-add-language-analyzers.md) | 複合語または文字列のハイフン、母音の変化、および動詞の形を保持します。 クエリ パターンにダッシュが含まれている場合は、言語アナライザーを使用するだけで十分な場合があります。 |
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | フィールド全体の内容は、1 つの用語としてトークン化されます。 |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 空白文字のみを区切ります。 ダッシュまたはその他の文字を含む用語は、1 つのトークンとして扱われます。 |
| [カスタム アナライザー](index-add-custom-analyzers.md) | (推薦) カスタム アナライザーを作成すると、トークナイザーとトークン フィルターの両方を指定できます。 前述のアナライザーはそのまま使用する必要があります。 カスタム アナライザーでは、使用するトークナイザーとトークン フィルターを選択できます。 <br><br>推奨される組み合わせは、[小文字のトークン フィルター](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)と[キーワード トークナイザー](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)です。 定義済みの[キーワード アナライザー](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)単体では大文字のテキストを小文字に変換できないため、クエリが失敗する可能性があります。 カスタム アナライザーには、小文字のトークン フィルターを追加するためのメカニズムが用意されています。 |

Postman のような Web API テスト ツールを使用している場合は、[テスト アナライザーの REST 呼び出し](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)を追加して、トークン化された出力を検査できます。

これを行うには既存のインデックスが必要です。 既存のインデックスと、ダッシュまたは用語の一部を含むフィールドが指定されている場合は、特定の用語に対してさまざまなアナライザーを試して、どのトークンが生成されるかを確認できます。  

1. Standard アナライザーを使用して、用語が既定でどのようにトークン化されているかを確認します。

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. 応答を評価して、インデックス内でテキストがどのようにトークン化されるかを確認します。 各用語がどのように小文字に変換され、分割されているかに注目してください。

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. `whitespace` または `keyword` アナライザーを使用するように要求を変更します。

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. これで、応答は大文字の 1 つのトークンで構成され、文字列の一部としてダッシュが保持されます。 パターンまたは用語の一部を検索する必要がある場合、これでクエリ エンジンが一致を見つけるための基礎となります。


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> 多くの場合、クエリ ツリーの構築時に、クエリ パーサーにより検索式内の用語が小文字に変換されることに注意してください。 テキスト入力を小文字に変換しないアナライザーを使用しており、期待した結果が得られない場合は、これが原因である可能性があります。 この問題を解決するため、以下の「カスタム アナライザーを使用する」セクションで説明されているように、小文字のトークン フィルターを追加します。

## <a name="configure-an-analyzer"></a>アナライザーを構成する
 
アナライザーを評価する場合も、特定の構成を使用する場合も、アナライザーをフィールド定義に指定する必要があります。また、組み込みのアナライザーを使用しない場合は、アナライザー自体を構成することもできます。 アナライザーをスワップする場合は、通常、インデックスを再構築 (削除、再作成、および再読み込み) する必要があります。 

### <a name="use-built-in-analyzers"></a>組み込みアナライザーを使用する

組み込みまたは定義済みのアナライザーは、フィールド定義の `analyzer` プロパティに名前を使用して指定できます。インデックスに追加の構成は必要ありません。 次の例では、フィールドに `whitespace` アナライザーを設定する方法を示します。 

他のシナリオと、他の組み込みアナライザーの詳細については、[定義済みアナライザー リスト](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)に関するページを参照してください。 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>カスタム アナライザーを使用する

[カスタム アナライザー](index-add-custom-analyzers.md)を使用している場合は、ユーザー定義のトークナイザー、トークン フィルターに可能な構成設定を組み合わせてインデックスで定義します。 次に、組み込みアナライザーの場合と同様に、フィールド定義で参照します。

用語全体のトークン化が目的である場合は、**キーワード トークナイザー**で構成されるカスタム アナライザーと**小文字のトークン フィルター**を選択することをお勧めします。

+ キーワード トークナイザーは、フィールドの内容全体に対して 1 つのトークンを作成します。
+ lowercase トークン フィルターは、大文字を小文字に変換します。 通常、クエリ パーサーでは、大文字のテキスト入力が小文字になります。 小文字への変換により、トークン化された用語を使用して入力を均質化します。

次の例は、キーワード トークナイザーと lowercase トークン フィルターを提供するカスタム アナライザーを示しています。

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> `keyword_v2` トークナイザーおよび `lowercase` トークン フィルターはシステムで認識され、既定の構成を使用しています。そのため、最初に定義しなくても名前で参照できます。

## <a name="build-and-test"></a>ビルドおよびテスト

シナリオをサポートするアナライザーとフィールド定義を使ってインデックスを定義したら、部分的な文字列クエリをテストできるように、代表的な文字列を含むドキュメントを読み込みます。 

前のセクションではロジックについて説明しました。 このセクションでは、ソリューションをテストする際に呼び出す必要がある各 API について説明します。 前述のように、Postman など対話型の Web テストツールを使用すると、これらのタスクを迅速に実行できます。

+ [[インデックスの削除]](https://docs.microsoft.com/rest/api/searchservice/delete-index) では、同じ名前の既存のインデックスが削除されるため、再び作成できるようになります。

+ [[インデックスの作成]](https://docs.microsoft.com/rest/api/searchservice/create-index) では、アナライザーの定義やアナライザーの仕様を含むフィールドなど、検索サービスでインデックス構造が作成されます。

+ [[ドキュメントの読み込み]](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) では、インデックスと同じ構造のドキュメントおよび検索可能なコンテンツをインポートします。 この手順の後、インデックスはクエリやテストを実行できるようになります。

+ [テスト アナライザー](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)については「[アナライザーの選択](#choose-an-analyzer)」で紹介しました。 さまざまなアナライザーを使用して、インデックスの一部の文字列をテストし、用語のトークン化方法を理解します。

+ [[ドキュメントの検索]](https://docs.microsoft.com/rest/api/searchservice/search-documents) では、ワイルドカードと正規表現について[単純な構文](query-simple-syntax.md)または[完全な Lucene 構文](query-lucene-syntax.md)のいずれかを使用してクエリ要求を作成する方法を説明します。

  "+1 (425) 703-6214" に対して "3-6214" のクエリを実行して一致を検索するなど、部分的な用語クエリの場合は、単純な構文 (`search=3-6214&queryType=simple`) を使用できます。

  "alphanumeric" に対して "num" または "numeric" のクエリを実行して一致を検索するなど、インフィックスおよびサフィックス クエリの場合は、完全な Lucene 構文と正規表現 (`search=/.*num.*/&queryType=full`) を使用します。

## <a name="tips-and-best-practices"></a>ヒントとベスト プラクティス

### <a name="tune-query-performance"></a>クエリ パフォーマンスの調整

Keyword_v2 トークナイザーと小文字のトークン フィルターを含む推奨構成を実装する場合、インデックス内の既存のトークンに対する追加のトークン フィルター処理が原因で、クエリのパフォーマンスが低下することがあります。 

次の例では、[EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) を追加して、プレフィックスを高速に一致させるようにします。 文字を含む 2 から 25 文字の組み合わせに対して、追加のトークンが生成されます (MS、MSF、MSFT、MSFT/、MSFT/S、MSFT/SQ、MSFT/SQL に限らない)。 ご想像のとおり、追加のトークン化によってインデックスが大きくなります。

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>インデックス作成とクエリ処理に別のアナライザーを使用する

アナライザーは、インデックスの作成時とクエリの実行中に呼び出されます。 どちらの場合も同じアナライザーを使用するのが一般的ですが、各ワークロードに対してカスタム アナライザーを構成できます。 アナライザーのオーバーライドは `analyzers` セクションの [インデックス定義](https://docs.microsoft.com/rest/api/searchservice/create-index)で指定され、その後特定のフィールドで参照されます。 

インデックス作成中にのみカスタム分析を行う必要がある場合は、カスタム アナライザーをインデックス作成のみに適用し、標準の Lucene アナライザー (または別のアナライザー) を使用してクエリを続行できます。

ロール固有の分析を指定するには、各プロパティのフィールドにプロパティを設定し、既定の `analyzer` プロパティの代わりに `indexAnalyzer` と `searchAnalyzer` を設定します。

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>次のステップ

この記事では、アナライザーがどのようにクエリの問題に寄与し、クエリの問題を解決するかについて説明します。 次の手順として、アナライザーによるインデックス作成とクエリ処理への影響について詳しく見ていきます。 特に、Analyze Text API を使用してトークン化された出力を返すことを検討してください。これにより、アナライザーがインデックス用に作成した内容を正確に確認できます。

+ [言語アナライザー](search-language-support.md)
+ [Azure Cognitive Search でのテキスト処理のためのアナライザー](search-analyzers.md)
+ [Text API の分析 (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [フルテキスト検索のしくみ (クエリ アーキテクチャ)](search-lucene-query-architecture.md)