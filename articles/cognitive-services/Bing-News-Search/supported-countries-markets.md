---
title: Azure の Bing News Search API でサポートされている国/地域と言語 | Microsoft Docs
description: Bing Image Search API でサポートされている国/地域と言語を確認します。
services: cognitive-services
author: MikeDodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 12/20/2017
ms.author: v-gedod
ms.openlocfilehash: 1cdacc82b680407814ff2d88e8ed43deacfb17d0
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001662"
---
# <a name="bing-news-search-countriesregions-and-languages"></a>Bing News Search でサポートされている国/地域と言語

Bing News Search API では多数の国/地域がサポートされ、その多くで複数の言語が使用されています。 クエリを使用して国/地域を指定すると、主に、その国/地域への関心に基づいて検索結果を絞り込むことができます。 さらに、これらの結果には Bing へのリンクを含めることができ、これらのリンクにより、指定された国/地域または言語に従って Bing ユーザー エクスペリエンスをローカライズできます。

`cc` クエリ パラメーターを使用して国/地域を指定できます。 国/地域を指定する場合は、`Accept-Language` HTTP ヘッダーを使用して 1 つ以上の言語コードも指定する必要があります。 サポートされる言語は国/地域によって異なります。これらは、「市場」の表に国/地域ごとに示されています。

または、`mkt` クエリ パラメーターと「**市場**」の表のコードを使用して市場を指定できます。 市場を指定すると、同時に国/地域と優先言語が指定されます。 この場合、`setLang` クエリ パラメーターは言語コードに設定されます。通常、ユーザーが別の言語で Bing を表示するよう設定しない限り、これは `mkt` によって指定される言語と同じになります。

## <a name="supported-markets-for-news-search-endpoint"></a>ニュース検索エンドポイントでサポートされるマーケット

`/news/search` エンドポイントについて、`mkt` クエリ パラメーターを指定するために使用できる市場コード値の一覧を次の表に示します。 Bing はこれらの市場に対してのみコンテンツを返します。 一覧は変更されることがあります。  
  
`cc` クエリ パラメーターで指定できる国/地域コードの一覧については、「[国番号](#countrycodes)」を参照してください。  
  
|国/リージョン|Language|市場コード|  
|---------------------|--------------|-----------------| 
|デンマーク|デンマーク語|da-DK|
|オーストリア|ドイツ語|de-AT| 
|スイス|ドイツ語|de-CH|
|ドイツ|ドイツ語|de-DE|
|オーストラリア|英語|en-AU|
|カナダ|英語|en-CA|
|イギリス|英語|en-GB|
|インドネシア|英語|en-ID|
|アイルランド|英語|en-IE|
|インド|英語|en-IN|
|マレーシア|英語|en-MY|
|ニュージーランド|英語|en-NZ|
|フィリピン共和国|英語|en-PH|
|シンガポール|英語|en-SG|
|米国|英語|en-US|
|英語|全般|en-WW|
|英語|全般|en-XA|
|南アフリカ|英語|en-ZA|
|アルゼンチン|スペイン語|es-AR|
|チリ|スペイン語|es-CL|
|スペイン|スペイン語|es-ES|
|メキシコ|スペイン語|es-MX|
|米国|スペイン語|es-US| 
|スペイン語|全般|es-XL|
|フィンランド|フィンランド語|fi-FI|  
|フランス|フランス語|fr-BE|
|カナダ|フランス語|fr-CA| 
|ベルギー|オランダ語|nl-BE|
|スイス|フランス語|fr-CH|
|フランス|フランス語|fr-FR|  
|イタリア|イタリア語|it-IT| 
|香港特別行政区|繁体字中国語|zh-HK|  
|台湾|繁体字中国語|zh-TW|
|日本|日本語|ja-JP|  
|韓国|韓国語|ko-KR|  
|オランダ|オランダ語|nl-NL|  
|中華人民共和国|中国語|zh-CN|  
|ブラジル|ポルトガル語|pt-BR|
|ロシア|ロシア語|ru-RU|  
|スウェーデン|スウェーデン語|sv-SE|  
|トルコ|トルコ語|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>ニュース エンドポイントでサポートされるマーケット
`/news` エンドポイントについて、`mkt` クエリ パラメーターを指定するために使用できる市場コード値の一覧を次の表に示します。 Bing はこれらの市場に対してのみコンテンツを返します。 一覧は変更されることがあります。  
  
`cc` クエリ パラメーターで指定できる国/地域コードの一覧については、「[国番号](#countrycodes)」を参照してください。  
  
|国/リージョン|Language|市場コード|  
|---------------------|--------------|-----------------| 
|デンマーク|デンマーク語|da-DK|
|ドイツ|ドイツ語|de-DE|
|オーストラリア|英語|en-AU|
|イギリス|英語|en-GB|
|米国|英語|en-US|
|英語|全般|en-WW|
|チリ|スペイン語|es-CL|
|メキシコ|スペイン語|es-MX|
|米国|スペイン語|es-US| 
|フィンランド|フィンランド語|fi-FI|  
|カナダ|フランス語|fr-CA|
|フランス|フランス語|fr-FR|  
|イタリア|イタリア語|it-IT| 
|ブラジル|ポルトガル語|pt-BR|
|中華人民共和国|中国語|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>ニュース トレンド エンドポイントでサポートされるマーケット
`/news/trendingtopics` エンドポイントについて、`mkt` クエリ パラメーターを指定するために使用できる市場コード値の一覧を次の表に示します。 Bing はこれらの市場に対してのみコンテンツを返します。 一覧は変更されることがあります。  
  
`cc` クエリ パラメーターで指定できる国/地域コードの一覧については、「[国番号](#countrycodes)」を参照してください。  
  
|国/リージョン|Language|市場コード|  
|---------------------|--------------|-----------------| 
|ドイツ|ドイツ語|de-DE|
|オーストラリア|英語|en-AU|
|イギリス|英語|en-GB|
|米国|英語|en-US|
|カナダ|英語|en-CA|
|インド|英語|en-IN|
|フランス|フランス語|fr-FR|
|カナダ|フランス語|fr-CA|
|ブラジル|ポルトガル語|pt-BR|
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
|カナダ|CA|  
|チリ|CL|  
|デンマーク|DK|  
|フィンランド|FI|  
|フランス|FR|  
|ドイツ|DE|  
|香港特別行政区|HK|  
|インド|IN|  
|インドネシア|ID|  
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
|米国|US|

## <a name="next-steps"></a>次の手順
Bing News Search エンドポイントについて詳しくは、[News Search API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference)をご覧ください。