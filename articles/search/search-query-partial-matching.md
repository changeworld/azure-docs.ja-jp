---
title: パターンと特殊文字の照合
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search クエリ要求で、ワイルドカードとプレフィックスのクエリを使用して、完全な用語または用語の一部を照合します。 一致させるのが難しい、特殊文字を含むパターンを完全に一致させるには、完全なクエリ構文とカスタム アナライザーを使用して解決できます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ec1422d03cce78bdd8206f6687a78b63ddf989dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75984927"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>パターンと特殊文字 (ダッシュ) の一致

特殊文字 (`-, *, (, ), /, \, =`) を含むクエリや、長い用語の一部の用語に基づくクエリ パターンの場合は、通常、追加の構成手順を実行して、予想する内容がインデックスに適切な形式で含まれていることを確認する必要があります。 

既定では、`+1 (425) 703-6214` のような電話番号は、`"1"`、`"425"`、`"703"`、`"6214"` としてトークン化されます。 ご想像のとおり、`"3-62"` の検索、ダッシュを含む用語の一部の検索は失敗します。これは、そのような内容が実際にはインデックスに存在しないためです。 

文字列の一部または特殊文字を検索する必要がある場合は、単純なトークン化規則で動作するカスタム アナライザーを使用して既定のアナライザーをオーバーライドできます。これは、クエリ文字列に用語の一部、または特殊文字が含まれている場合に必要となります。 少し前に戻ると、手順は次のようになります。

+ 定義済みアナライザーを選択するか、必要な出力を生成するカスタム アナライザーを定義する
+ フィールドにアナライザーを割り当てる
+ インデックスを作成してテストする

この記事では、タスクの手順について説明します。 ここで説明する方法は、他のシナリオで役に立ちます。ワイルドカードと正規表現クエリには、パターン マッチングの基礎として、完全な用語も必要です。 

> [!TIP]
> 分析を評価することは、インデックスの再構築を頻繁に行う必要がある反復的なプロセスです。 [インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index)、[インデックスの削除](https://docs.microsoft.com/rest/api/searchservice/delete-index)、[ドキュメントの読み込み](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)、および[ドキュメントの検索](https://docs.microsoft.com/rest/api/searchservice/search-documents)の REST API である Postman を使用することで、これらの手順を簡単に行うことができます。 ドキュメントの読み込みの場合、要求本文には、テストする代表的な小さいデータセットが含まれている必要があります (たとえば、電話番号や製品コードを含むフィールドなど)。 同じ Postman コレクション内のこれらの API を使用すると、これらの手順を迅速に実行できます。

## <a name="choosing-an-analyzer"></a>アナライザーの選択

用語全体のトークンを生成するアナライザーを選択する場合は、次のアナライザーが一般的な選択肢となります。

| アナライザー | 動作 |
|----------|-----------|
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | フィールド全体の内容は、1 つの用語としてトークン化されます。 |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 空白文字のみを区切ります。 ダッシュまたはその他の文字を含む用語は、1 つのトークンとして扱われます。 |
| [カスタム アナライザー](index-add-custom-analyzers.md) | (推薦) カスタム アナライザーを作成すると、トークナイザーとトークン フィルターの両方を指定できます。 前述のアナライザーはそのまま使用する必要があります。 カスタム アナライザーでは、使用するトークナイザーとトークン フィルターを選択できます。 <br><br>推奨される組み合わせは、[小文字のトークン フィルター](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)と[キーワード トークナイザー](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)です。 定義済みの[キーワード アナライザー](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)単体では大文字のテキストを小文字に変換できないため、クエリが失敗する可能性があります。 カスタム アナライザーには、小文字のトークン フィルターを追加するためのメカニズムが用意されています。 |

Postman のような Web API テスト ツールを使用している場合は、[テスト アナライザーの REST 呼び出し](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)を追加して、トークン化された出力を検査できます。 既存のインデックスと、ダッシュまたは用語の一部を含むフィールドが指定されている場合は、特定の用語に対してさまざまなアナライザーを試して、どのトークンが生成されるかを確認できます。  

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
> 多くの場合、クエリ ツリーの構築時に、クエリ パーサーにより検索式内の用語が小文字に変換されることに注意してください。 テキスト入力を小文字に変換しないアナライザーを使用しており、期待した結果が得られない場合は、これが原因である可能性があります。 解決策として、小文字のトークン フィルターを追加します。

## <a name="analyzer-definitions"></a>アナライザーの定義
 
アナライザーを評価する場合も、特定の構成を使用する場合も、アナライザーをフィールド定義に指定する必要があります。また、組み込みアナライザーを使用しない場合は、アナライザー自体を構成します。 アナライザーをスワップする場合は、通常、インデックスを再構築 (削除、再作成、および再読み込み) する必要があります。 

### <a name="use-built-in-analyzers"></a>組み込みアナライザーを使用する

組み込みまたは定義済みのアナライザーは、フィールド定義の `analyzer` プロパティに名前を使用して指定できます。インデックスに追加の構成は必要ありません。 次の例では、フィールドに `whitespace` アナライザーを設定する方法を示します。

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
使用できるすべての組み込みアナライザーの詳細については、[定義済みアナライザーの一覧](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)に関する記事を参照してください。 

### <a name="use-custom-analyzers"></a>カスタム アナライザーを使用する

[カスタム アナライザー](index-add-custom-analyzers.md)を使用している場合は、使用可能な構成設定と共に、ユーザー定義の tokenizer、tokenfilter の組み合わせによりインデックスに定義します。 次に、組み込みアナライザーの場合と同様に、フィールド定義で参照します。

用語全体のトークン化が目的である場合は、**キーワード トークナイザー**で構成されるカスタム アナライザーと**小文字のトークン フィルター**を選択することをお勧めします。

+ キーワード トークナイザーは、フィールドの内容全体に対して 1 つのトークンを作成します。
+ lowercase トークン フィルターは、大文字を小文字に変換します。 通常、クエリ パーサーでは、大文字のテキスト入力が小文字に変換されます。 小文字に変換することで、トークン化された用語を使用して入力を均質化します。

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
]

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
]

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

### <a name="duplicate-fields-for-different-scenarios"></a>さまざまなシナリオでフィールドを複製する

別のオプションでは、フィールドごとのアナライザー割り当てを利用して、さまざまなシナリオに合わせて最適化します。 具体的には、最初のフルテキスト検索をサポートするために "featureCode" と "featureCodeRegex" を定義し、2 番目に高度なパターン マッチングを適用することができます。

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
  "analyzer": "my_customanalyzer"
},
```

## <a name="next-steps"></a>次のステップ

この記事では、アナライザーがどのようにクエリの問題に寄与し、クエリの問題を解決するかについて説明します。 次の手順として、アナライザーによるインデックス作成とクエリ処理への影響について詳しく見ていきます。 特に、Analyze Text API を使用してトークン化された出力を返すことを検討してください。これにより、アナライザーがインデックス用に作成した内容を正確に確認できます。

+ [言語アナライザー](search-language-support.md)
+ [Azure Cognitive Search でのテキスト処理のためのアナライザー](search-analyzers.md)
+ [Text API の分析 (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [フルテキスト検索のしくみ (クエリ アーキテクチャ)](search-lucene-query-architecture.md)