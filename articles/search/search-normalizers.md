---
title: フィルター、ファセット、並べ替えのためのテキスト正規化
titleSuffix: Azure Cognitive Search
description: インデックス内のテキスト フィールドにノーマライザーを指定して、フィルター処理、ファセット、および並べ替えでの厳密なキーワード一致動作をカスタマイズします。
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: 3e7a33d9213d7af44d2cfc50baa847534618f7e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608704"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>大文字と小文字を区別しないフィルター処理、ファセット、並べ替えのためのテキスト正規化

 > [!IMPORTANT]
 > ノーマライザーはパブリック プレビュー段階にあり、**2020-06-30-preview REST API** を介してのみ利用できます。 プレビュー機能は、補足利用規約に基づいて、現状のまま提供されます。

Azure Cognitive Search インデックスからドキュメントを検索および取得するには、ドキュメントの内容に対してクエリを一致させる必要があります。 `search` パラメーターを使用した場合のように内容を分析して一致させるトークンを生成することも、あるいは、`$filter`、`facets`、および `$orderby` を使用した場合のように、厳密なキーワード一致の対象として内容をそのまま使用することもできます。 このようなオール オア ナッシングのやり方でもほとんどのシナリオに対応できますが、分析チェーン全体を経由せずに、大文字と小文字の区別、アクセントの削除、Asciifolding などの簡単な事前処理を行う必要がある場合はうまくいきません。

次に例を示します。

+ `$filter=City eq 'Las Vegas'` は、厳密に "Las Vegas" というテキストを含むドキュメントだけを返し、"LAS VEGAS" や "las vegas" を含むドキュメントを除外するため、大文字と小文字の区別に関係なくすべてのドキュメントが必要なユースケースでは不十分です。

+ `search=*&facet=City,count:5` は、同じ都市であっても、"Las Vegas"、"LAS VEGAS"、および "las vegas" を別個の値として返します。

+ `search=usa&$orderby=City` は、大文字か小文字かに関係なく、同じ都市をまとめて並べたいという意図であったとしても、"Las Vegas"、"Seattle"、"las vegas" という辞書式順序で都市を返します。 

## <a name="normalizers"></a>ノーマライザー

"*ノーマライザー*" は検索エンジンのコンポーネントで、キーワード一致に備えたテキストの事前処理を担います。 ノーマライザーは、アナライザーに似ていますが、クエリをトークン化しない点が異なります。 ノーマライザーを使用して実現できる変換の例:

+ 文字列をすべて小文字または大文字に変換する。
+ Ö や ê のようなアクセントおよび分音記号を、ASCII 文字である "o" や "e" に正規化する。
+ `-` や空白をユーザーが指定した文字にマップする。

ノーマライザーは、インデックス内のテキスト フィールドに指定でき、インデックス作成とクエリ実行の両方に適用されます。

## <a name="predefined-and-custom-normalizers"></a>定義済みおよびカスタム ノーマライザー 

Azure Cognitive Search は、一般的なユースケースに対応した定義済みノーマライザーのサポートに加え、必要に応じてカスタマイズできる機能もサポートしています。

| カテゴリ | 説明 |
|----------|-------------|
| [定義済みノーマライザー](#predefined-normalizers) | すぐに使用できる状態で提供されるため、構成が不要です。 |
|[カスタム ノーマライザー](#add-custom-normalizers) | 高度なシナリオ向けです。 文字フィルターとトークン フィルターで構成される既存要素の組み合わせをユーザーが定義する必要があります。<sup>1</sup>|

<sup>(1)</sup> カスタム ノーマライザーは常に 1 つのトークンを生成するため、トークナイザーの指定は不要です。

## <a name="how-to-specify-normalizers"></a>ノーマライザーの指定方法

ノーマライザーは、`filterable`、`sortable`、または `facetable` プロパティの少なくとも 1 つが true に設定されているテキスト フィールド (`Edm.String` および `Collection(Edm.String)`) に対してフィールドごとに指定できます。 ノーマライザーの設定は省略可能であり、既定では `null` です。 カスタム ノーマライザーを構成する前に、より使いやすい定義済みノーマライザーを評価することをお勧めします。 結果が期待できなかった場合に、カスタム ノーマライザーをお試しください。

ノーマライザーは、新しいフィールドがインデックスに追加されたときのみ指定できます。 正規化のニーズを前もって評価し、インデックスの削除と再作成がルーチンとなってしまうような場合は、開発の初期段階でノーマライザーを割り当てることをお勧めします。 既に作成済みのフィールドにノーマライザーを指定することはできません。

1. [インデックス](/rest/api/searchservice/create-index)にフィールド定義を作成する場合は、**normalizer** プロパティを、`lowercase` などの[定義済みノーマライザー](#predefined-normalizers)またはカスタム ノーマライザー (同じインデックス スキーマで定義) のいずれかに設定します。  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. カスタム ノーマライザーは、インデックスの **[normalizers]** セクションで最初に定義し、次に、前の手順で示したようにフィールド定義に割り当てる必要があります。 詳細については、[インデックスの作成](/rest/api/searchservice/create-index)に関する記事、および「[カスタム ノーマライザーを追加する](#add-custom-normalizers)」を参照してください。


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> 既存のフィールドのノーマライザーを変更するには、インデックスを完全に再作成する必要があります (個々のフィールドを再作成することはできません)。

インデックス再作成の負担が大きい実稼働インデックスで推奨される回避策としては、古いフィールドに指定されていたものと同じノーマライザーを指定して新たにフィールドを作成し、これを古いフィールドの代わりに使用する方法があります。 新しいフィールドを組み込むには [Update Index](/rest/api/searchservice/update-index) を使用し、それを事前設定するには [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) を使用してください。 後で、計画的なインデックス サービスの一環として、インデックスをクリーンアップし、不要になったフィールドを削除することができます。

## <a name="add-custom-normalizers"></a>カスタム ノーマライザーを追加する

カスタム ノーマライザーは、インデックス スキーマ内で定義し、フィールド プロパティを使用して指定できます。 カスタム ノーマライザーの定義には、名前、型、1 つ以上の文字フィルター、およびトークン フィルターが含まれます。 文字フィルターとトークン フィルターは、カスタム ノーマライザーの構成要素であり、テキストの処理を担います。これらのフィルターは左から右に適用されます。

 `token_filter_name_1` はトークン フィルターの名前、`char_filter_name_1` と `char_filter_name_2` は文字フィルターの名前です (有効な値については、「[サポートされるトークン フィルター](#supported-token-filters)」および下の文字フィルターの表をご覧ください)。

ノーマライザーの定義は、より大きなインデックス定義の一部です。 インデックスの REST については、[Create Index API (インデックス作成 API)](/rest/api/searchservice/create-index) に関する記事をご覧ください。

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

カスタム ノーマライザーは、インデックスの作成中に追加することも、既存のインデックスを更新することによって後から追加することもできます。 既存のインデックスにカスタム ノーマライザーを追加するには、[インデックスを更新する](/rest/api/searchservice/update-index)際に **allowIndexDowntime** フラグを指定する必要があります。これにより、数秒間インデックスが使用できなくなります。

## <a name="normalizers-reference"></a>ノーマライザーのリファレンス

### <a name="predefined-normalizers"></a>定義済みノーマライザー
|**名前**|**説明とオプション**|  
|-|-|  
|standard| 後に asciifolding が続くテキストを小文字に変換します。|  
|lowercase| 文字を小文字に変換します。|
|大文字| 文字を大文字に変換します。|
|asciifolding| 基本ラテン Unicode ブロックに含まれていない文字が存在する場合は、それに対応する ASCII 値に変換します。 たとえば、à は a に変更されます。|
|elision| トークンの先頭から省略を削除します。|

### <a name="supported-char-filters"></a>サポートされている文字フィルター
文字フィルターの詳細については、[文字フィルターのリファレンス](index-add-custom-analyzers.md#CharFilter)を参照してください。
+ [mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>サポートされているトークン フィルター
次の一覧に、ノーマライザーでサポートされているトークン フィルターを示します。これは、字句解析に関係するトークン フィルター全体のサブセットです。 フィルターの詳細については、[トークン フィルターのリファレンス](index-add-custom-analyzers.md#TokenFilters)を参照してください。

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [lowercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [uppercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>カスタム ノーマライザーの例

次の例は、対応する文字フィルターとトークン フィルターを使用したカスタム ノーマライザーの定義を示しています。 文字フィルターとトークン フィルターのカスタム オプションは、名前付きコンストラクトとして個別に指定され、以下で説明するようにノーマライザーの定義で参照されます。

* カスタム ノーマライザー "my_custom_normalizer" は、インデックス定義の `normalizers` セクションで定義される。
* ノーマライザーは、2 つの文字フィルターと、3 つのトークン フィルター (elision、lowercase、カスタマイズされた asciifolding フィルター "my_asciifolding") で構成される。
* 1 つ目の文字フィルター "map_dash" はすべてのダッシュをアンダースコアで置き換え、2 つ目 "remove_whitespace" はすべての空白を削除する。

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
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
```

## <a name="see-also"></a>関連項目
+ [言語処理とテキスト処理のためのアナライザー](search-analyzers.md)

+ [Search Documents REST API](/rest/api/searchservice/search-documents) 
