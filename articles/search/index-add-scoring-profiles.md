---
title: スコアリング プロファイルを追加して検索スコアを上げる
titleSuffix: Azure Cognitive Search
description: 検索インデックスにスコアリング プロファイルを追加することにより、Azure Cognitive Search の検索結果での検索の関連性スコアを上げます。
manager: nitinme
author: shmed
ms.author: ramero
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: 757290560f921ee87cf9454037a4f8f199283f29
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449490"
---
# <a name="add-scoring-profiles-to-a-search-index"></a>検索インデックスにスコアリング プロファイルを追加する

フルテキスト検索クエリの場合、検索エンジンは一致する各ドキュメントの検索スコアを計算します。これにより、結果を高から低にランク付けできます。 Azure Cognitive Search では、既定のスコアリング アルゴリズムを使用して初期スコアを計算しますが、"*スコアリング プロファイル*" を介して計算をカスタマイズすることができます。

スコアリング プロファイルはインデックス定義に埋め込まれており、一致のスコアを上げるためのプロパティが含まれています。プロファイルに含まれる追加の条件によってブースト ロジックが提供されます。 たとえば、収益の見込みに基づいて一致をブーストしたり、より新しい項目のレベルを上げたり、場合によっては在庫期間が長すぎる項目をブーストしたりできます。  

関連性の概念について詳しく理解するには、 次のビデオ セグメントを参照してください。Azure Cognitive Search でのスコアリング プロファイルの動作についての部分まで早送りされます。このビデオでは、基本的な概念についても説明しています。 また、より詳しい背景情報を[類似性ランク付けとスコアリング](index-similarity-and-scoring.md)に関する記事で確認することもできます。

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=463&end=970]

## <a name="what-is-a-scoring-profile"></a>スコアリング プロファイルとは

スコアリング プロファイルは、重み付けされたフィールド、関数、およびパラメーターで構成されるインデックス定義の一部です。 スコアリング プロファイルの目的は、指定した条件に基づいて一致するドキュメント数を増加または増幅することです。 

次の定義は、"geo" という名前のシンプルなプロファイルを示しています。 このプロファイルは、hotelName フィールド内の検索語句を含む結果をブーストします。 また、`distance` 関数を使用して、現在の場所から 10 キロメートル以内にある結果を優先します。 他のユーザーが語句 "inn" の検索を実行し、"inn" がたまたまホテル名の一部であった場合、現在位置の半径 10 Km 以内にあって "inn" を含むホテルが記載されているドキュメントが検索結果で上位に表示されます。  

```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  

このスコアリング プロファイルを使用するには、クエリを作成して、要求に scoringProfile パラメーターを指定します。

```http
POST /indexes/hotels/docs&api-version=2020-06-30
{
    "search": "inn",
    "scoringProfile": "geo",
    "scoringParameter": currentLocation--122.123,44.77233
}
```  

このクエリは、語句 "inn" を検索し、現在の場所を渡します。 このクエリには、scoringParameter などの他のパラメーターが含まれていることがわかります。 クエリ パラメーターについては、「[ドキュメントの検索 (REST API)](/rest/api/searchservice/Search-Documents)」を参照してください。  

スコアリング プロファイルの詳細な例を確認するには、「[拡張の例](#bkmk_ex)」を参照してください。  

<a name=what-is-default-scoring></a>

## <a name="how-scores-are-computed"></a>スコアの計算方法

スコアは、フルテキスト検索クエリに対して、関連性が最も高い一致をランク付けし、それを応答の先頭で返すために計算されます。 各ドキュメントの全体のスコアは、各フィールドの個々のスコアを集計したものです。各フィールドの個々のスコアは、対象のフィールド内で検索される語句の出現頻度と文書頻度 ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) (語句の出現頻度と逆文書頻度) とも呼ばれます) に基づいて計算されます。 

> [!Tip]
> [featuresMode](index-similarity-and-scoring.md#featuresmode-parameter-preview) パラメーターを使用して、検索結果 (フィールド レベルのスコアを含む) と共に追加のスコアリングの詳細を要求できます。

## <a name="when-to-add-scoring-logic"></a>スコアリング ロジックを追加する場合

既定の順位付けの動作ではビジネス目標を達成するに至らない場合、1 つ以上のスコアリング プロファイルを作成する必要があります。 たとえば、検索の関連性で、新しく追加された項目を優先するとします。 同様に、利益率を含んでいるフィールド、または他に収益の可能性を示す何らかのフィールドがあるとします。 多くの場合、ユーザーやビジネスにとって意味のあるブースティング結果はスコアリング プロファイルの導入の決定的要因になります。

検索ページにおける関連性に基づく順序付けも、スコアリング プロファイルを通じて実装されます。 価格、日付、評価、または関連性での並べ替えが可能な、以前に使用した検索結果ページを検討してしてください。 Azure Cognitive Search では、スコアリング プロファイルを使用して "関連性" オプションが開始されます。 関連性の定義は、ビジネス目標および提供する検索機能の種類に基づいて、ユーザーによって制御されます。  

<a name="bkmk_ex"></a>

## <a name="extended-example"></a>拡張の例

次の例では、2 つのスコアリング プロファイル (`boostGenre`、`newAndHighlyRated`) を使用するインデックス スキーマを示します。 いずれかのプロファイルをクエリ パラメーターとして指定したクエリを、このインデックスに対して実行すると、該当するプロファイルを使用して結果セットのスコアリングが行われます。 

`boostGenre` プロファイルでは、重み付けされたテキスト フィールドを使用して、albumTitle、genre、artistName の各フィールドで検出された一致をブーストします。 これらのフィールドはそれぞれ 1.5、5、および 2 でブーストされます。 なぜ genre は他のものよりも大幅に高くブーストされるのでしょうか? ある程度同じようなデータに対して検索を実行する場合 (musicstoreindex での "genre" の場合のように)、相対的な重みをより分散することが必要になる可能性があります。 たとえば、musicstoreindex では、"ロック" はジャンルとして表示されると共に、言葉で表現されるジャンルの説明の中にも表示されます。 ジャンルがジャンルの説明を上回るようにする場合は、genre フィールドの相対的な重みをより高くする必要があります。

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="steps-for-adding-a-scoring-profile"></a>スコアリング プロファイルを追加する手順

カスタムのスコアリング動作を実装するには、インデックスを定義するスキーマにスコアリング プロファイルを追加します。 インデックスには最大 100 個のスコアリング プロファイルを含めることができます ([サービスの制限](search-limits-quotas-capacity.md)に関する記事を参照) が、クエリには一度に 1 つのプロファイルのみ指定できます。

1. 最初にインデックス定義を作成します。 既存のインデックスでスコアリング プロファイルを追加および更新できます。インデックスを再構築する必要はありません。 [インデックスの更新](/rest/api/searchservice/update-index)要求を使用してリビジョンをポストします。

1. このトピックで提供される[テンプレート](#bkmk_template)に貼り付けます。  

1. 名前を指定します。 スコアリング プロファイルは省略できますが、追加する場合は、名前が必要です Cognitive Search のフィールドの[名前付け規則](/rest/api/searchservice/naming-rules)に従ってください (文字で始め、特殊文字や予約語の使用は避ける)。  

1. ブースト条件を指定します。 1 つのプロファイルには、[重み付けされたフィールド](#weighted-fields)と[関数](#functions)、またはその両方を含めることができます。 

特定のプロファイルの有効性を証明または誤りを証明するために役立つデータ セットを使用して、反復的な作業を行う必要があります。

次のスクリーンショットに示すように、スコアリング プロファイルは Azure portal で定義できます。また、[REST API](/rest/api/searchservice/update-index) や Azure SDK (Azure SDK for .NET の [ScoringProfile](/dotnet/api/azure.search.documents.indexes.models.scoringprofile) クラスなど) を使用してプログラムから定義することもできます。

   :::image type="content" source="media/scoring-profiles/portal-add-scoring-profile-small.png" alt-text="[スコアリング プロファイルの追加] ページ" lightbox="media/scoring-profiles/portal-add-scoring-profile.png" border="true":::

<a name="weighted-fields"></a>

### <a name="using-weighted-fields"></a>重み付けされたフィールドの使用

フィールド コンテキストが重要であり、クエリがフルテキスト検索 (自由形式のテキスト検索とも呼ばれる) である場合は、重み付けされたフィールドを使用します。 たとえば、クエリに "airport" という語句が含まれる場合は、Description フィールドの "airport" の重みを HotelName よりも大きく設定できます。 

重み付けされたフィールドは、検索可能なフィールドおよび乗数として使用される正の数で構成されます。 HotelName の元のフィールド スコアが 3 の場合、そのフィールドのブーストされたスコアは 6 になり、親ドキュメント自体の全体のスコアが高くなります。

```json
"scoringProfiles": [  
{  
  "name": "boostKeywords",  
  "text": {  
    "weights": {  
      "HotelName": 2,  
      "Description": 5 
    }  
  }  
}
```

<a name="functions"></a>

### <a name="using-functions"></a>関数の使用

数値データに対する計算である距離や鮮度の場合のように、単純かつ相対的な重みが不足している場合、または適用されない場合は、関数を使用します。 スコアリング プロファイルごとに複数の関数を指定できます。

| 機能 | 説明 |
|-|-|
| "freshness" | datetime フィールド (Edm.DateTimeOffset) の値によってブーストします。 この関数には `boostingDuration` 属性が含まれているので、ブーストが発生する期間を表す値を指定できます。 | 
| "magnitude" | 数値の高低に基づいてブーストします。 この関数を呼び出すシナリオとしては、利益率、最高価格、最低価格、またはダウンロード回数に基づくブーストがあります。 この関数は、Edm.Double フィールドと Edm.Int フィールドでのみ使用できます。 magnitude 関数では、逆のパターンが必要な場合 (たとえば、価格がより高い項目より価格がより低い項目をブーストする場合) に、高範囲から低範囲に反転することができます。 たとえば、価格の範囲が 100 ～ 1 ドルである場合、"boostingRangeStart" を 100 に、"boostingRangeEnd" を 1 に設定して、より低い価格の項目をブーストします。 | 
| "distance"  | 近接または地理的な場所によってブーストします。 この関数は、Edm.GeographyPoint フィールドでのみ使用できます。 | 
| "tag"  | 検索ドキュメントとクエリ文字列の両方に共通するタグによってブーストします。 タグは `tagsParameter` で指定されます。 この関数は、Edm.String フィールドと Collection(Edm.String) フィールドでのみ使用できます。 | 

### <a name="rules-for-using-functions"></a>関数の使用に関する規則

+ 関数はフィルター設定可能の属性が付けられているフィールドにのみ適用できます。
+ 関数型 ("freshness"、"magnitude"、"distance"、"tag") は小文字にする必要があります。
+ 関数に null または空の値を含めることはできません。

<a name="bkmk_template"></a>

## <a name="template"></a>テンプレート

 このセクションでは、スコアリング プロファイルの構文とテンプレートを示します。 スコアリング プロファイルの属性の説明については、次のセクションの「[プロパティ リファレンス](#bkmk_indexref)」を参照してください。  

```json
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
        "fieldName": "(...)",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...", 
```  

<a name="bkmk_indexref"></a> 

## <a name="property-reference"></a>プロパティ リファレンス

|属性|説明|  
|---------------|-----------------|  
| name | 必須。 これは、スコアリング プロファイルの名前です。 これは、フィールドの場合と同じ名前付け規則に従います。 先頭は英文字にする必要があります。ドット、コロン、または @ 記号を含めることはできません。さらに、azureSearch (大文字小文字の区別あり) という句で開始することはできません。|  
| text | 重みプロパティが含まれます。|  
| weights | 省略可能。 検索可能なフィールドと、フィールドのスコアを上げるための正の整数または浮動小数点数を指定する名前と値のペア。 正の整数または数値は、ランク付けアルゴリズムによって生成された元のフィールド スコアの乗数になります。 たとえば、フィールド スコアが 2 で重みの値が 3 の場合、そのフィールドのブーストされたスコアは 6 になります。 続いて、ドキュメントのフィールド スコアを作成するために個々のフィールド スコアが集計されます。これは、結果セット内のドキュメントのランク付けに使用されます。 |  
| functions | 省略可能。 スコアリング関数はフィルターの適用が可能なフィールドにのみ適用できます。|  
| functions > type | スコアリング関数の場合は必須です。 使用する関数の型を示します。 有効な値は、magnitude、freshness、distance、tag です。 各スコアリング プロファイルには複数の関数を含めることができます。 関数名は小文字にする必要があります。|  
| functions > boost | スコアリング関数の場合は必須です。 生のスコアの乗数として使用される正の数値。 1 にすることはできません。|  
| functions > fieldname | スコアリング関数の場合は必須です。 スコアリング関数は、インデックスのフィールド コレクションに属し、フィルターの適用が可能なフィールドにのみ適用できます。 さらに、各関数型には追加の制限 (freshness は日時フィールドで使用され、magnitude は整数または倍精度浮動小数点フィールドで使用され、distance は場所フィールドで使用される) が導入されています。 関数の定義ごとに指定できるフィールドは 1 つだけです。 たとえば、同じプロファイルで magnitude を 2 回使用するには、magnitude の定義を 2 つ含める必要があります (フィールドごとに 1 つ)。|  
| functions > interpolation | スコアリング関数の場合は必須です。 スコア ブースティングが範囲の始点から範囲の終点に向かって高くなる場合の傾斜を定義します。 有効な値は、Linear (既定値)、Constant、Quadratic、Logarithmic です。 詳細については、「 [補間の設定](#bkmk_interpolation) 」を参照してください。|  
| functions > magnitude | 数値フィールドの値の範囲に基づいて順位付けを変更するには、magnitude スコアリング関数を使用します。 最も一般的な使用例には、次のようなものがあります。 </br></br>"星評価": "星評価" フィールド内の値に基づいて、スコアリングを変更します。 2 つの項目が該当する場合は、評価の高い方の項目が最初に表示されます。 </br>"利益": 2 つのドキュメントが該当する場合、小売業者は利益が高い方のドキュメントを最初にブーストできます。 </br>"クリック回数": 製品やページに対するクリック操作を追跡するアプリケーションの場合は、magnitude を使用することで、最大トラフィックを示す傾向にある項目をブーストできます。 </br>"ダウンロード回数": ダウンロードを追跡するアプリケーションの場合は、magnitude 関数を使用することで、ダウンロード回数が最も多い項目をブーストできます。|  
| functions > magnitude > boostingRangeStart | magnitude をスコアリングする範囲の開始値を設定します。 値は整数または浮動小数点数にする必要があります。 1 ～ 4 の星評価を対象とする場合、これは 1 になります。 50% を超える利益を対象とする場合、これは 50 になります。|  
| functions > magnitude > boostingRangeEnd | magnitude をスコアリングする範囲の終了値を設定します。 値は整数または浮動小数点数にする必要があります。 1 ～ 4 の星評価を対象とする場合、これは 4 になります。|  
| functions > magnitude > constantBoostBeyondRange | 有効な値は、true または false (既定値) です。 true に設定すると、対象フィールドの値が範囲の上限を超えているドキュメントに、フル ブーストが継続的に適用されます。 false に設定すると、対象フィールドの値が範囲外にあるドキュメントに対して、この関数のブーストは適用されません。|  
| functions > freshness | DateTimeOffset フィールドの値に基づいて項目の順位付けのスコアを変更するには、freshness スコアリング関数を使用します。 たとえば、最近の日付を持つ項目に、より古い日付を持つ項目よりも高い順位を付けることができます。 </br></br>将来の日付を使用して、カレンダー イベントなどの項目に順位を付けることもできます。たとえば、現在に近い日付の項目を遠い日付の項目よりも高い順位にすることができます。 </br></br>現在のサービス リリースでは、範囲の一端が現在の時刻に固定されます。 もう一端は、boostingDuration に基づく過去の時刻です。 将来の時刻の範囲をブーストするには、負の値の boostingDuration を使用します。 </br></br>最大および最小の範囲からのブースティングの変化率は、スコアリング プロファイルに適用した補間によって決定されます (下図参照)。 適用されるブースティング係数を反転するには、ブースト係数として 1 未満を選択します。|  
| functions > freshness > boostingDuration | 特定のドキュメントに対して有効期限を設定します。この期限を過ぎるとブースティングは停止します。 構文と例については、以下のセクション「[boostingDuration の設定](#bkmk_boostdur)」を参照してください。|  
| functions > distance | 地理的な参照場所との間の距離に基づいてドキュメントのスコアに影響を与えるには、distance スコアリング関数を使用します。 参照場所は、クエリの一部としてパラメーター (scoringParameter クエリ パラメーターを使用) 内に lon,lat 引数で指定します。|  
|functions >  distance > referencePointParameter | 参照位置として使用する、クエリで渡されるパラメーター (scoringParameter クエリ パラメーターを使用)。 クエリ パラメーターの説明については、「[ドキュメントの検索 (REST API)](/rest/api/searchservice/Search-Documents)」を参照してください。|  
| functions > distance > boostingDistance | ブースティング範囲が終了する、参照場所からの距離をキロメートルで示す数値。|  
| functions > tag | ドキュメントと検索クエリのタグに基づいてドキュメントのスコアに影響を与えるには、tag スコアリング関数を使用します。 検索クエリと共通のタグを持つドキュメントがブーストされます。 検索クエリのタグは、(scoringParameter クエリ パラメーターを使用して) 各検索要求でスコアリング パラメーターとして提供されます。 |  
| functions > tag > tagsParameter | 特定の要求のタグを指定する、クエリで渡されるパラメーター (scoringParameter クエリ パラメーターを使用)。 クエリ パラメーターの説明については、「[ドキュメントの検索 (REST API)](/rest/api/searchservice/Search-Documents)」を参照してください。|  
| functions > functionAggregation | 省略可能。 関数を指定した場合にのみ適用されます。 有効な値は、sum (既定値)、average、minimum、maximum、firstMatching です。 検索スコアは、複数の変数 (複数の関数など) から計算される単一の値です。 この属性では、すべての関数のブーストを、基本ドキュメント スコアに適用される 1 つの集計ブーストに集約する方法が示されます。 基本スコアは、ドキュメントと検索クエリから算出される [tf-idf](http://www.tfidf.com/) 値に基づいています。|  
| defaultScoringProfile | 検索要求の実行時に、スコアリング プロファイルが指定されていない場合は、既定のスコアリングが使用されます ([tf-idf](http://www.tfidf.com/) のみ)。 </br></br>組み込みの既定値をオーバーライドして、カスタム プロファイル (検索要求に特定のプロファイルが指定されていない場合に使用します) に置き換えることができます。|  

<a name="bkmk_interpolation"></a> 

## <a name="set-interpolations"></a>補間の設定

補間を使用することにより、スコアリングに使用される傾きの形状を設定できます。 スコアリングは高から低なので、傾きは常に減少ですが、補間により下向きの傾きの曲線が決まります。 次の補間を使用できます。  

| 補間 | 説明 |  
|-|-|  
|`linear`|最大および最小の範囲内にある項目の場合、項目に適用されるブーストは一定の減少量で行われます。 線形はスコアリング プロファイルの既定の補間です。|  
|`constant`|開始と終了の範囲内にある項目については、一定のブーストが順位の結果に適用されます。|  
|`quadratic`|ブーストが一定の割合で低下する線形補間と比較して、二次方程式の場合は、最初は遅いペースで低下しますが、終了範囲に近づくにつれて低下するペースが速くなります。 tag スコアリング関数では、この補間オプションは使用できません。|  
|`logarithmic`|ブーストが一定の割合で低下する線形補間と比較して、対数の場合は、最初は速いペースで低下しますが、終了範囲に近づくにつれて低下するペースが遅くなります。 tag スコアリング関数では、この補間オプションは使用できません。|  

 ![グラフでの定数、線形、二次方程式、log10 の線](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

<a name="bkmk_boostdur"></a> 

## <a name="set-boostingduration"></a>boostingDuration の設定

`boostingDuration` は `freshness` 関数の属性です。 これを使用して特定のドキュメントに対して有効期限を設定します。この期限が過ぎるとブースティングは停止します。 たとえば、10 日のプロモーション期間中に製品ラインまたはブランドをブーストするには、該当するドキュメントに対して 10 日の期間を "P10D" で指定します。  

`boostingDuration` は、XSD "dayTimeDuration" 値 (ISO 8601 期間値の制限されたサブセット) としてフォーマットする必要があります。 これのパターンは次のとおりです:"P[nD][T[nH][nM][nS]]"。  

次の表に、いくつかの例を示します。  

|Duration|boostingDuration|  
|--------------|----------------------|  
|1 日|"P1D"|  
|2 日と 12 時間|"P2DT12H"|  
|約 15 分|"PT15M"|  
|30 日 と 5 時間 10 分 6.334 秒|"P30DT5H10M6.334S"|  

他の例については、「[XML Schema:Datatypes (XML スキーマ: データ型)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)」(W3.org の Web サイト) をご覧ください。  

## <a name="see-also"></a>関連項目

+ [Azure Cognitive Search での類似性ランク付けとスコアリング](index-similarity-and-scoring.md)
+ [REST API リファレンス](/rest/api/searchservice/)
+ [インデックスの作成 API](/rest/api/searchservice/create-index)
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search?)
+ [スコアリング プロファイルとは](https://social.technet.microsoft.com/wiki/contents/articles/26706.azure-search-what-are-scoring-profiles.aspx)
