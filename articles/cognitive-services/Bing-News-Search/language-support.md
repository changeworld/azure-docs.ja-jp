---
title: 言語サポート - Bing News Search API
titleSuffix: Azure Cognitive Services
description: Bing News Search API でサポートされる自然言語、国、および地域の一覧。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: d15058126f43fff328acfc563ffd081164a69a90
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "79218877"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Bing News Search API の言語と地域のサポート

Bing News Search API では多数の国/地域がサポートされ、その多くで複数の言語が使用されています。 クエリを使用して国/地域を指定すると、主に、その国/地域への関心に基づいて検索結果を絞り込むことができます。 さらに、これらの結果には Bing へのリンクを含めることができ、これらのリンクにより、指定された国/地域または言語に従って Bing ユーザー エクスペリエンスをローカライズできます。

`cc` クエリ パラメーターを使用して国/地域を指定できます。 国/地域を指定する場合は、`Accept-Language` HTTP ヘッダーを使用して 1 つ以上の言語コードも指定する必要があります。 サポートされる言語は国/地域によって異なります。これらは、「市場」の表に国/地域ごとに示されています。

または、`mkt` クエリ パラメーターと「**市場**」の表のコードを使用して市場を指定できます。 市場を指定すると、同時に国/地域と優先言語が指定されます。 この場合、`setLang` クエリ パラメーターは言語コードに設定されます。通常、ユーザーが別の言語で Bing を表示するよう設定しない限り、これは `mkt` によって指定される言語と同じになります。

## <a name="supported-markets-for-news-search-endpoint"></a>ニュース検索エンドポイントでサポートされるマーケット

`/news/search` エンドポイントについて、`mkt` クエリ パラメーターを指定するために使用できる市場コード値の一覧を次の表に示します。 Bing はこれらの市場に対してのみコンテンツを返します。 一覧は変更されることがあります。  

`cc` クエリ パラメーターで指定できる国/地域コードの一覧については、「[国番号](#countrycodes)」を参照してください。  

|国/リージョン|言語|市場コード|  
|---------------------|--------------|-----------------|
|デンマーク|Danish|da-DK|
|オーストリア|German|de-AT|
|スイス|German|de-CH|
|ドイツ|German|de-DE|
|オーストラリア|English|en-AU|
|Canada|English|en-CA|
|イギリス|English|en-GB|
|インドネシア|English|en-ID|
|アイルランド|English|en-IE|
|インド|English|en-IN|
|マレーシア|English|en-MY|
|ニュージーランド|English|en-NZ|
|フィリピン共和国|English|en-PH|
|シンガポール|English|en-SG|
|United States|English|ja-JP|
|English|全般|en-WW|
|English|全般|en-XA|
|南アフリカ|English|en-ZA|
|アルゼンチン|Spanish|es-AR|
|チリ|Spanish|es-CL|
|スペイン|Spanish|es-ES|
|メキシコ|Spanish|es-MX|
|United States|Spanish|es-US|
|Spanish|全般|es-XL|
|フィンランド|Finnish|fi-FI|  
|フランス|French|fr-BE|
|Canada|French|fr-CA|
|ベルギー|Dutch|nl-BE|
|スイス|French|fr-CH|
|フランス|French|fr-FR|  
|イタリア|Italian|it-IT|
|香港特別行政区|Traditional Chinese|zh-HK|  
|台湾|Traditional Chinese|zh-TW|
|日本|Japanese|ja-JP|  
|韓国|Korean|ko-KR|  
|オランダ|Dutch|nl-NL|  
|中華人民共和国|中国語|zh-CN|  
|ブラジル|Portuguese|pt-BR|
|ロシア|Russian|ru-RU|  
|スウェーデン|Swedish|sv-SE|  
|トルコ|Turkish|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>ニュース エンドポイントでサポートされるマーケット
`/news` エンドポイントについて、`mkt` クエリ パラメーターを指定するために使用できる市場コード値の一覧を次の表に示します。 Bing はこれらの市場に対してのみコンテンツを返します。 一覧は変更されることがあります。  

`cc` クエリ パラメーターで指定できる国/地域コードの一覧については、「[国番号](#countrycodes)」を参照してください。  

|国/リージョン|言語|市場コード|  
|---------------------|--------------|-----------------|
|デンマーク|Danish|da-DK|
|ドイツ|German|de-DE|
|オーストラリア|English|en-AU|
|イギリス|English|en-GB|
|United States|English|ja-JP|
|English|全般|en-WW|
|チリ|Spanish|es-CL|
|メキシコ|Spanish|es-MX|
|United States|Spanish|es-US|
|フィンランド|Finnish|fi-FI|  
|Canada|French|fr-CA|
|フランス|French|fr-FR|  
|イタリア|Italian|it-IT|
|ブラジル|Portuguese|pt-BR|
|中華人民共和国|中国語|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>ニュース トレンド エンドポイントでサポートされるマーケット
`/news/trendingtopics` エンドポイントについて、`mkt` クエリ パラメーターを指定するために使用できる市場コード値の一覧を次の表に示します。 Bing はこれらの市場に対してのみコンテンツを返します。 一覧は変更されることがあります。  

`cc` クエリ パラメーターで指定できる国/地域コードの一覧については、「[国番号](#countrycodes)」を参照してください。  

|国/リージョン|言語|市場コード|  
|---------------------|--------------|-----------------|
|ドイツ|German|de-DE|
|オーストラリア|English|en-AU|
|イギリス|English|en-GB|
|United States|English|ja-JP|
|Canada|English|en-CA|
|インド|English|en-IN|
|フランス|French|fr-FR|
|Canada|French|fr-CA|
|ブラジル|Portuguese|pt-BR|
|中華人民共和国|中国語|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>国コード  

`cc` クエリ パラメーターで指定できる国/地域コードを次に示します。 一覧は変更されることがあります。  

|国/リージョン|国番号|  
|---------------------|------------------|  
|アルゼンチン|AR|  
|オーストラリア|AU|  
|オーストリア|AT|  
|ベルギー|BE|  
|ブラジル|BR|  
|Canada|CA|  
|チリ|CL|  
|デンマーク|DK|  
|フィンランド|FI|  
|フランス|FR|  
|ドイツ|DE|  
|香港特別行政区|HK|  
|インド|IN|  
|インドネシア|id|  
|イタリア|IT|  
|日本|JP|  
|韓国|KR|  
|マレーシア|MY|  
|メキシコ|MX|  
|オランダ|NL|  
|ニュージーランド|NZ|  
|ノルウェー|NO|  
|中華人民共和国|CN|  
|ポーランド|PL|  
|ポルトガル|PT|  
|フィリピン共和国|PH|  
|ロシア|RU|  
|サウジアラビア|SA|  
|南アフリカ|ZA|  
|スペイン|ES|  
|スウェーデン|SE|  
|スイス|CH|  
|台湾|TW|  
|トルコ|TR|  
|イギリス|GB|  
|United States|US|

## <a name="next-steps"></a>次のステップ
Bing News Search エンドポイントについて詳しくは、[News Search API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)をご覧ください。
