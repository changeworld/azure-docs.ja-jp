---
title: "Azure Search のアナライザー | Microsoft Docs"
description: "インデックスの検索可能なテキスト フィールドにアナライザーを割り当てて、既定の標準 Lucene をカスタムの定義済みの代替または言語固有の代替と置換します。"
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/03/2017
ms.author: heidist
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: 6e6c4491b8f66011340d1246495dbded7caf2903
ms.contentlocale: ja-jp
ms.lasthandoff: 09/05/2017

---

# <a name="analyzers-in-azure-search"></a>Azure Search のアナライザー

*アナライザー*は、インデックスとクエリのワークロードの両方について、テキストからトークンへの変換を担当する[フルテキスト検索処理](search-lucene-query-architecture.md)のコンポーネントです。 インデックスの作成中に、アナライザーはテキストをトークン化された用語に変換し、それがインデックスに出力されます。 クエリ時に、アナライザーは同じ変換 (テキストからトークン化された用語) を実行しますが、今度はクエリ時の読み取り操作です。 

分析中は、次の変換が一般的です。

+ 重要ではない単語 (ストップワード) と感嘆符は削除されます。
+ フレーズとハイフンでつながれた用語は、構成要素に分割されます。
+ 用語は小文字に変換されます。
+ 単語は、時制に関係なく一致が見つかるように、原形に戻されます。

Azure Search には既定のアナライザーがあります。 フィールドごとに代替の選択肢で上書きすることができます。 この記事の目的は、選択肢の範囲と、検索操作にアナライザーを追加する場合のベスト プラクティスについて説明することです。 また、主なシナリオについてアナライザーの構成例についても示します。

## <a name="how-analysis-fits-into-full-text-search-processing"></a>分析をフルテキスト検索処理に合わせる方法

アナライザーは、クエリ パーサーから渡される用語の入力に対して動作し、クエリ ツリー オブジェクトに追加される分析済みの用語を返します。

 ![Azure Search における Lucene クエリ アーキテクチャの図][1]

アナライザーは、単一用語のクエリまたはフレーズ クエリにのみ使用されます。 語句全体を必要としない種類のクエリ (プレフィックス クエリ、ワイルドカード クエリ、正規表現クエリなど) やあいまい検索には、アナライザーは使用されません。 このような種類のクエリの場合、分析段階をバイパスして、クエリ ツリーに用語が直接追加されます。 こうした種類の検索語に対して適用される変換は、大文字から小文字への変換だけです。

## <a name="supported-analyzers"></a>サポートされているアナライザー

次の一覧では、Azure Search でサポートされているアナライザーについて説明しています。

| カテゴリ | Description |
|----------|-------------|
| [標準 Lucene のアナライザー](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | [既定]。 インデックス作成とクエリに自動的に使用されます。 指定や構成は必要ありません。 この汎用アナライザーは、ほとんどの言語とシナリオで適切に実行されます。|
| 定義済みアナライザー | そのまま使用するように完成した製品として提供されます。カスタマイズは制限されています。 <br/>特殊と言語という 2 種類があります。 "定義済み" とは、カスタマイズなしで、名前で参照するためです。 <br/><br/>[特殊 (言語を選ばない) アナライザー](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable)。特殊な処理または最小限の処理が必要なテキスト入力に使用します。 非言語の定義済みアナライザーには、**Asciifolding**、**Keyword**、**Pattern**、**Simple**、**Stop**、**Whitespace** などがあります。<br/><br/>[言語アナライザー](https://docs.microsoft.com/rest/api/searchservice/language-support)は、個々の言語に合わせて豊富な言語のサポート提供しています。 Azure Search は、35 個の Lucene 言語アナライザーと 50 個の Microsoft 自然言語処理アナライザーをサポートしています。 |
|[カスタム アナライザー](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | 1 つのトークナイザー (必須) と省略可能なフィルター (文字またはトークン) から構成される既存の要素を組み合わせたユーザー定義の構成。|

**Pattern** や **Stop** などの定義済みアナライザーをカスタマイズして、「[Predefined Analyzer Reference](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable)」(定義済みアナライザー リファレンス) で説明されている代替オプションを使用できます。 設定できるオプションがあるのは、定義済みアナライザーのごく一部です。 他のカスタマイズと同様に、新しい構成に *myPatternAnalyzer* などの名前を付けて、Lucene パターン アナライザーの名前を区別できるようにします。

## <a name="how-to-specify-analyzer"></a>アナライザーを指定する方法

1. カスタム アナライザーの場合にのみ、インデックスの `analyzer` 定義を作成します。 詳細については、「[Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)」(インデックスの作成) と[「Custom Analyzers」(カスタム アナライザー) の「Create」(作成)](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer) も参照してください。

2. アナライザーを使用する各フィールドで、[インデックスのフィールド定義](https://docs.microsoft.com/rest/api/searchservice/create-index)のターゲット アナライザー名に `analyzer` プロパティを設定します。 有効な値には、定義済みアナライザー、またはインデックス スキーマで定義した言語アナライザー、カスタム アナライザーが含まれます。

 また、1 つの `analyzer` プロパティの代わりに、`indexAnalyzer` および `searchAnalyzer` フィールド パラメーターを使用して、インデックスとクエリに別のアナライザーを設定することができます。 

3. 新しいテキスト処理動作を呼び出すには、インデックスを再構築します。

## <a name="best-practices"></a>ベスト プラクティス

このセクションでは、アナライザーをより効率的に使用する方法についてアドバイスを提供します。

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>特定の要件がない場合は読み取り/書き込みに 1 つのアナライザー

Azure Search では、追加の `indexAnalyzer` および `searchAnalyzer` フィールド パラメーターを使用して、インデックス作成と検索に別々のアナライザーを指定することができます。 指定しなかった場合、`analyzer` プロパティで設定されたアナライザーが、インデックス作成と検索の両方に使用されます。 `analyzer` が指定されなかった場合は、標準の Lucene アナライザーが使用されます。

一般的な規則は、特定の要件で別に指示がない場合は、インデックスとクエリの両方に同じアナライザーを使用することです。 通常、トークンを作成するアナライザーが、後でクエリ時にトークンの検索に使用されるアナライザーと同じである方が効率的です。 

### <a name="test-during-active-development"></a>アクティブな開発中のテスト

標準アナライザーを上書きするには、インデックスの再構築が必要です。 可能であれば、アクティブな開発中に使用するアナライザーを決めてから、インデックスを運用環境に展開します。

### <a name="compare-analyzers-side-by-side"></a>アナライザーの並列比較

[Analyze API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) を使用することをお勧めします。 応答は、提供したテキスト用のアナライザーから生成される、トークン化された用語で構成されます。 

> [!Tip]
> [Search Analyzer Demo](http://alice.unearth.ai/) では、標準 Lucene アナライザー、Lucene の英語アナライザー、Microsoft の英語自然言語プロセッサの比較が横並びに表示されます。 指定した検索の入力ごとに、各アナライザーの結果が隣接する列に表示されます。

## <a name="examples"></a>例

いくつかの主なシナリオについて、アナライザーの定義例を示します。

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>例 1: カスタム オプション

この例では、カスタム オプションを使用してアナライザー定義を示しています。 文字フィルター、トークナイザー、トークン フィルターのオム オプションは、名前付きコンストラクトとは別に指定され、アナライザー定義で参照されます。 定義済みの要素はそのまま使用され、単純に名前で参照されます。

この例について手順を説明します。

* アナライザーは、検索可能なフィールドのフィールド クラスのプロパティです。
* カスタム アナライザーは、インデックス定義の一部です。 軽くカスタマイズされているか (1 つのフィルターの 1 つのオプションがカスタマイズされているなど)、複数個所でカスタマイズされている可能性があります。
* この例で、カスタム アナライザーは "my_analyzer" です。このアナライザーはカスタマイズされた標準トークナイザー "my_standard_tokenizer" と、小文字とカスタマイズされた asciifolding フィルターである "my_asciifolding" という 2 つのトークン フィルターを使用しています。
* また、トークン化前にすべてのダッシュをアンダースコアに置換するカスタム "map_dash" 文字フィルターも定義します (標準のトークナイザーはダッシュで停止しますが、アンダースコアでは停止しません)。

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Example2"></a>
### <a name="example-2-override-the-default-analyzer"></a>例 2: 既定のアナライザーを上書きする

標準アナライザーが既定です。 たとえば、既定のアナライザーを、パターン アナライザーなど、別の定義済みアナライザーで置き換えるとします。 カスタム オプションを設定していない場合、フィールド定義で名前を使用して指定する必要があります。

"analyzer" 要素は、フィールドごとに標準アナライザーを上書きします。 グローバルな上書きはありません。 この例で、`text1` はパターン アナライザーと `text2` を使用します。これはアナライザーを指定せず、既定値を使用します。

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Example3"></a>
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>例 3: インデックス作成と検索操作に別のアナライザーを指定する

プレビュー API には、インデックス作成と検索に別のアナライザーを指定するための追加のインデックス属性が含まれています。 ペアとして `searchAnalyzer` 属性と `indexAnalyzer` 属性を指定して、1 つの `analyzer` 属性を置き換える必要があります。


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Example4"></a>
### <a name="example-4-language-analyzer"></a>例 4: 言語アナライザー

複数言語の文字列を含むフィールドでは、言語アナライザーを使用できますが、他のフィールドは既定値のままです (または、他の定義済みまたはカスタム アナライザーを使用します)。 言語アナライザーを使用する場合、インデックス作成と検索操作の両方に使用する必要があります。 言語アナライザーを使用するフィールドでは、インデックス作成と検索に別のアナライザーを使用することはできません。

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "IndexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="next-steps"></a>次のステップ

+ 包括的な説明については、「[Azure Search のフルテキスト検索のしくみ](search-lucene-query-architecture.md)」を参照してください。 この記事では、例を使って、表面上は直感的ではないと思われるような動作について説明しています。

+ [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) の例に関するセクションや[単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)で紹介されているさまざまなクエリ構文をポータルの Search エクスプローラーで試します。

+ [言語に固有の字句解析器](https://docs.microsoft.com/rest/api/searchservice/language-support)を適用する方法について書かれた記事を参照します。

+ 個々のフィールドに対して最小限の処理または特殊な処理を適用するための[カスタム アナライザーを構成](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)します。

+ このデモ Web サイトで隣接する列の[標準と英語のアナライザーを比較](http://alice.unearth.ai/)します。 

## <a name="see-also"></a>関連項目

 [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Full Lucene クエリ構文](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [検索結果の処理方法](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png

