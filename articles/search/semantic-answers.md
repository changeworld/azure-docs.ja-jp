---
title: セマンティック回答を返す
titleSuffix: Azure Cognitive Search
description: セマンティック回答の構成と、結果セットから回答を得る方法について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 9bb62544887e0bc0269b98cd98fbf97fc477352f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722431"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>Azure Cognitive Search でセマンティック回答を返す

> [!IMPORTANT]
> セマンティック検索はパブリック プレビュー段階にあり、プレビュー REST API を介してのみ利用できます。 プレビュー機能は、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)下で現状のまま提供されており、一般提供時に同じ実装があるとは限りません。 これらの機能は課金対象です。 詳細については、[可用性と価格](semantic-search-overview.md#availability-and-pricing)に関するページを参照してください。

[セマンティック クエリ](semantic-how-to-query-request.md)を作成するときに、必要に応じて、クエリに直接 "回答" する上位のドキュメントからコンテンツを抽出できます。 1 つ以上の回答を応答に含めることができます。これは、アプリのユーザー エクスペリエンスを向上させるために、検索ページに表示できます。

この記事では、セマンティック回答を要求する方法、応答を分解する方法、および高品質な回答を生成するために最も役立つコンテンツ特性について説明します。

## <a name="prerequisites"></a>前提条件

[セマンティック クエリ](semantic-how-to-query-request.md)に適用されるすべての前提条件は、サービス レベルやリージョンなどの回答にも適用されます。

+ クエリ ロジックには、セマンティック クエリ パラメーターに加え、"answers" パラメーターが含まれている必要があります。 この記事では、必須パラメーターについて説明します。

+ ユーザーによって入力されたクエリ文字列は、質問の特性 (何、どこ、いつ、どのように) を含んだ言葉で作成する必要があります。

+ 検索ドキュメントには、回答の特性を持つテキストが含まれている必要があります。また、テキストは、"searchFields" にリストされているフィールドのいずれかにある必要があります。 たとえば、"what is a hash table (ハッシュ テーブルとは何か)" というクエリが与えられたとき、"A hash table is ... (ハッシュ テーブルとは ...)" という節がどの searchFields にも含まれていない場合、回答が返される可能性は低くなります。

## <a name="what-is-a-semantic-answer"></a>セマンティック回答とは

セマンティック回答は、[セマンティック クエリ応答](semantic-how-to-query-request.md)の基礎です。 検索ドキュメントからの 1 つ以上の逐語的な節で構成され、質問のようなクエリに対する回答として作成されます。 回答を返すには、、回答の言語特性を持つ語句または文が検索ドキュメントに存在する必要があります。また、クエリ自体を質問として表す必要があります。

Cognitive Search は、機械読み取りの理解モデルを使用して最適な回答を選択します。 このモデルでは、利用可能なコンテンツから一連の潜在的な回答が生成され、十分な信頼性レベルに達すると回答が提案されます。

回答は、クエリ応答ペイロード内で独立した最上位オブジェクトとして返されます。これを検索結果と共に検索ページに表示するように選択できます。 構造的には、テキスト、ドキュメント キー、信頼度スコアから成る応答内の配列要素です。

<a name="query-params"></a>

## <a name="how-to-request-semantic-answers-in-a-query"></a>クエリでセマンティック回答を要求する方法

セマンティック回答を返すには、クエリにセマンティック "queryType"、"queryLanguage"、"searchFields"、"answers" の各パラメーターが必要です。 "answers" パラメーターを指定しても、回答が得られるとは限りませんが、回答処理を呼び出すには、要求にこのパラメーターを含める必要があります。

"searchFields" パラメーターは、コンテンツと順序の両面で高品質の回答を返すために不可欠です (下記参照)。 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ クエリ文字列を null にすることはできません。また、質問の形式で作成する必要があります。 このプレビューでは、例とまったく同じように "queryType" と "queryLanguage" を設定する必要があります。

+ "searchFields" パラメーターによって、抽出モデルにトークンを提供する文字列フィールドが決まります。 回答は、キャプションを生成する同じフィールドからも生成されます。 キャプションと回答のどちらでも機能するようにこのフィールドを設定する方法についての詳細なガイダンスについては、「[searchFields を設定する](semantic-how-to-query-request.md#searchfields)」を参照してください。 

+ "answers" の場合、パラメーターの構造は `"answers": "extractive"` です。ここで、返される回答の既定の数は 1 です。 上の例のように、上限を 5 としてカウントを追加することで回答の数を増やすことができます。  複数の回答が必要かどうかは、アプリのユーザー エクスペリエンスと、結果の表示方法によって異なります。

## <a name="deconstruct-an-answer-from-the-response"></a>応答から回答を分解する

回答は、@search.answers 配列で提供されます。この配列は、応答の最初に表示されます。 回答が不確定の場合には、応答は `"@search.answers": []` として表示されます。 回答を含む検索結果ページを設計するときは、回答が見つからない場合の対処方法を含めてください。

@search.answers 内では、"key" は一致するドキュメントのキーまたは ID です。 ドキュメント キーが指定されている場合は、[Lookup Document](/rest/api/searchservice/lookup-document) API を使用して、検索ページまたは詳細ページに含める検索ドキュメントの任意またはすべての部分を取得できます。

"text" と "highlights" はどちらも、提供するコンテンツは同じで、プレーン テキストと強調表示したものです。 既定では、強調表示は `<em>` としてスタイル設定されます。これは、既存の highlightPreTag パラメーターと highlightPostTag の各パラメーターを使用してオーバーライドできます。 他の場所に記載されているように、回答の内容は、検索ドキュメントからの逐語的コンテンツです。 抽出モデルは、回答の特性を検索して適切なコンテンツを検索しますが、応答で新しい文章を作成しません。

"score" は、回答の強度を示す信頼度スコアです。 応答に複数の回答がある場合は、このスコアで順序が決定されます。 上位の回答と上位のキャプションは、別々の検索ドキュメントから得られることがあります。上位の回答と上位のキャプションが別々のドキュメントから派生するというものですが、一般的には、各配列内の上位の位置には同じドキュメントが表示されます。

回答の後に "value" 配列が続きます。この配列には常に、スコア、キャプション、および既定で取得可能なあらゆるフィールドが含まれています。 select パラメーターを指定した場合、"value" 配列には指定したフィールドのみが含まれます。 応答の項目の詳細については、「[セマンティック クエリを作成する](semantic-how-to-query-request.md)」を参照してください。

クエリで "how do clouds form" と指定すると、応答で次のような回答が返されます。

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
        }
```

## <a name="tips-for-producing-high-quality-answers"></a>高品質な回答を得るためのヒント

最良の結果を得るためには、次の特性を持つドキュメント コーパスのセマンティック回答を返します。

+ "searchFields" には、回答が見つかる可能性の高い十分なテキストを提供するフィールドが指定されている必要があります。 回答として表示できるのは、ドキュメントからの逐語的なテキストだけです。

+ クエリ文字列を null (search=`*`) にすることはできません。また文字列は、キーワード検索 (任意の語句が連続したリスト) ではなく、質問の特性を持った文字列である必要があります。 クエリ文字列が回答のように見えない場合は、要求でクエリ パラメーターとして "answers" が指定されていても、回答処理はスキップされます。

+ セマンティック抽出と概要作成では、適切なタイミングで分析できるドキュメントあたりのトークン数が制限されています。 実際には、何百ページにもわたる大きなドキュメントがある場合、まずコンテンツをより小さいドキュメントに分割することをお勧めします。

## <a name="next-steps"></a>次のステップ

+ [セマンティック検索の概要](semantic-search-overview.md)
+ [セマンティック ランク付けアルゴリズム](semantic-ranking.md)
+ [類似性ランク付けアルゴリズム](index-ranking-similarity.md)
+ [セマンティック クエリの作成](semantic-how-to-query-request.md)