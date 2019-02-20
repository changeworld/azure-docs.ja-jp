---
title: インデックスの定義と概念を作成する - Azure Search
description: 構成要素や物理的構造など、Azure Search でのインデックスの用語と概念の概要を説明します。
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/01/2019
ms.custom: seodec2018
ms.openlocfilehash: 77f4b597ad4b87db7e720dd57191c6b192a4c93b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000952"
---
# <a name="create-a-basic-index-in-azure-search"></a>Azure Search で基本的なインデックスを作成する

Azure Search における*インデックス*とは、Azure Search サービスでのフィルターされたおよびフル テキストの検索に使用される*ドキュメント*などの構成要素の永続的なストアです。 概念的に、ドキュメントはインデックス内で検索可能なデータの 1 つの単位です。 たとえば、eコマースの小売業者であれば販売品目ごとにドキュメントがあり、報道機関であれば記事ごとにドキュメントがあります。 これらの概念をなじみのあるデータベースの同等のものに対応させるなら、*インデックス*は概念的には*テーブル*に似ており、*ドキュメント*はテーブルにおける*行*とほぼ同じです。

インデックスを追加またはアップロードすると、ユーザーが提供したスキーマを基に Azure Search によって物理構造が作成されます。 たとえば、インデックス内のフィールドが検索可能としてマークされていると、そのフィールドの転置インデックスが作成されます。 後で、ドキュメントを追加またはアップロードするとき、または検索クエリを Azure Search に送信するときは、検索サービス内の特定のインデックスに対して要求を送信していることになります。 フィールドにドキュメントの値を読み込むことを、"*インデックス付け*" またはデータ インジェストと呼びます。

インデックスの作成は、ポータル、[REST API](search-create-index-rest-api.md)、または [.NET SDK](search-create-index-dotnet.md) で行うことができます。

## <a name="components-of-an-index"></a>インデックスのコンポーネント

スキーマ的には、Azure Search のインデックスは次の要素で構成されます。 

"[*フィールド コレクション*](#fields-collection)" は通常、インデックスの最大の部分であり、各フィールドには、名前、型、および使用方法を決定する許容される動作を示す属性が設定されます。 他の要素としては、[suggester](#suggesters)、[スコアリング プロファイル](#scoring-profiles)、カスタマイズをサポートする構成要素を含む[アナライザー](#analyzers)、および [CORS](#cors) オプションがあります。

```json
{  
  "name": (optional on PUT; required on POST) "name_of_index",  
  "fields": [  
    {  
      "name": "name_of_field",  
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",  
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),  
      "filterable": true (default) | false,  
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),  
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),  
      "key": true | false (default, only Edm.String fields can be keys),  
      "retrievable": true (default) | false,  
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }  
  ],  
  "suggesters": [  
    {  
      "name": "name of suggester",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": ["field1", "field2", ...]  
    }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "name of scoring profile",  
      "text": (optional, only applies to searchable fields) {  
        "weights": {  
          "searchable_field_name": relative_weight_value (positive #'s),  
          ...  
        }  
      },  
      "functions": (optional) [  
        {  
          "type": "magnitude | freshness | distance | tag",  
          "boost": # (positive number used as multiplier for raw score != 1),  
          "fieldName": "...",  
          "interpolation": "constant | linear (default) | quadratic | logarithmic",  
          "magnitude": {  
            "boostingRangeStart": #,  
            "boostingRangeEnd": #,  
            "constantBoostBeyondRange": true | false (default)  
          },  
          "freshness": {  
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)  
          },  
          "distance": {  
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)  
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)  
          },  
          "tag": {  
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)  
          }  
        }  
      ],  
      "functionAggregation": (optional, applies only when functions are specified)   
        "sum (default) | average | minimum | maximum | firstMatching"  
    }  
  ],  
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",  
  "corsOptions": (optional) {  
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],  
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)  
  }  
}
```

## <a name="fields-collection-and-attribution"></a>フィールド コレクションと属性
スキーマを定義する際に、インデックスの各フィールドの名前、型、属性を指定する必要があります。 フィールドの型によって、そのフィールドに格納されているデータが分類されます。 属性は個々のフィールドに設定されてフィールドの使用方法を指定します。 次の表に、指定できる型と属性をまとめます。

### <a name="data-types"></a>データの種類
| type | 説明 |
| --- | --- |
| *Edm.String* |フルテキスト検索 (単語区切り、ステミングなど) のために必要に応じてトークン化することのできるテキスト。 |
| *Collection(Edm.String)* |フルテキスト検索のために必要に応じてトークン化することのできる一連の文字列。 コレクション内の項目の数に理論上の上限はありませんが、ペイロードのサイズに対する 16 MB の上限がコレクションに適用されます。 |
| *Edm.Boolean* |true または false の値が含まれます。 |
| *Edm.Int32* |32 ビット整数値です。 |
| *Edm.Int64* |64 ビット整数値です。 |
| *Edm.Double* |倍精度数値データです。 |
| *Edm.DateTimeOffset* |OData V4 形式で表された日時の値です (例: `yyyy-MM-ddTHH:mm:ss.fffZ` または `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`)。 |
| *Edm.GeographyPoint* |地球上の地理的な場所を表すポイントです。 |

Azure Search のサポートされるデータ型の詳細については、[このページ](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)を参照してください。

### <a name="index-attributes"></a>インデックスの属性
| Attribute | 説明 |
| --- | --- |
| *キー* |ドキュメント検索に使用される各ドキュメントの一意の ID を提供する文字列です。 各インデックスに、1 つのキーが必要です。 1 つのフィールドだけをキーにすることができ、その型を Edm.String に設定する必要があります。 |
| *Retrievable* |検索結果でフィールドを返すことができるかどうかを設定します。 |
| *Filterable* |フィルター クエリでフィールドを使用できるようにします。 |
| *Sortable* |このフィールドを使ってクエリで検索結果を並べ替えられるようにします。 |
| *Facetable* |ユーザー自律フィルター処理の [ファセット ナビゲーション](search-faceted-navigation.md) 構造でフィールドを使用できるようにします。 通常は、複数のドキュメント (たとえば、1 つのブランドやサービス カテゴリに属する複数のドキュメント) をまとめてグループ化するために使用できる、反復する値があるフィールドが、ファセットとして最適です。 |
| *Searchable* |フィールドをフルテキスト検索可能としてマークします。 |

Azure Search のインデックス属性の詳細については、[このページ](https://docs.microsoft.com/rest/api/searchservice/Create-Index)を参照してください。

## <a name="suggesters"></a>サジェスター
suggester は、検索においてオートコンプリートまたは先行入力クエリをサポートするために使用されるインデックス内のフィールドが定義されている、スキーマのセクションです。 通常、ユーザーが検索クエリを入力している間に部分的な検索文字列が検索候補 (Azure Search Service REST API) に送信されて、API からは検索候補の語句のセットが返されます。 インデックスで定義した suggester によって、先行入力検索語句を作成するために使用するフィールドが決められます。 詳しくは、[suggester の追加](index-add-suggesters.md)に関する記事をご覧ください。

## <a name="scoring-profiles"></a>スコアリング プロファイル

スコアリング プロファイルは、検索結果での項目の表示順序を変更できるカスタム スコアリング動作が定義されている、スキーマのセクションです。 スコアリング プロファイルは、フィールドの重みと関数で構成されます。 使用するには、クエリ文字列にプロファイル名を指定します。

既定のスコアリング プロファイルはバックグラウンドで実行され、検索セットの各項目の検索スコアがコンピューティングされます。 内部的な名前のないスコアリング プロファイルを使用できます。 または、クエリ文字列にカスタム プロファイルが指定されていない場合に常に呼び出される既定値として、カスタム プロファイルを使用するように defaultScoringProfile を設定します。

詳しくは、「[スコアリング プロファイルの追加](index-add-scoring-profiles.md)」をご覧ください。

## <a name="analyzers"></a>アナライザー

アナライザー要素では、フィールドに対して使用される言語アナライザーの名前が設定されます。 設定できる値については、[Azure Search での言語アナライザー](index-add-language-analyzers.md)に関する記事をご覧ください。 このオプションは、検索可能なフィールドでのみ使用でき、**searchAnalyzer** または **indexAnalyzer** と共に設定することはできません。 フィールドのアナライザーを選択した後は変更できません。

## <a name="cors"></a>CORS

ブラウザーではすべてのクロスオリジン要求が禁止されるので、既定ではクライアント側 JavaScript で API を呼び出すことはできません。 インデックスに対するクロスオリジン クエリを許可するには、**corsOptions** 属性を設定することによって、CORS (クロスオリジン リソース共有) を有効にします。 セキュリティ上の理由から、CORS がサポートされているのはクエリ API だけです。 

CORS に対しては以下のオプションを設定できます。

+ **allowedOrigins** (必須):インデックスへのアクセスが許可されるオリジンのリストです。 つまり、これらのオリジンから提供されるすべての JavaScript コードは、インデックスのクエリを許可されます (正しい api-key を提供する場合)。 各オリジンの標準的な形式は `protocol://<fully-qualified-domain-name>:<port>` ですが、多くの場合 `<port>` は省略されます。 詳しくは、「[Cross-origin resource sharing (クロスオリジン リソース共有)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)」(Wikipedia) をご覧ください。

  すべてのオリジンにアクセスを許可する場合は、**allowedOrigins** 配列の唯一の要素として `*` を指定します。 "*運用環境の検索サービスに対してはこれは推奨されません*" が、開発およびデバッグでは役に立つことがよくあります。

+ **maxAgeInSeconds** (省略可能):ブラウザーでは、この値を使用して、CORS プレフライト応答をキャッシュする期間 (秒) が決定されます。 負ではない整数を指定する必要があります。 この値が大きいほどパフォーマンスはよくなりますが、CORS ポリシーの変更が有効になるまでの時間は長くなります。 これを設定しないと、既定の期間として 5 分が使用されます。

## <a name="next-steps"></a>次の手順

インデックスの構成を理解したなら、続けてポータルで最初のインデックスを作成できます。

> [!div class="nextstepaction"]
> [インデックスを追加する (ポータル)](search-create-index-portal.md)