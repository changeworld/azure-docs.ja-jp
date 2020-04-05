---
title: 言語サポート - Bing Custom Search API
titleSuffix: Azure Cognitive Services
description: Bing Custom Search API でサポートされる言語と地域の一覧。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 85326ae9166f7ea15ec2f45c01755b8f9ef03aff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "66388564"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Bing Custom Search API の言語と地域のサポート

Bing Custom Search API では 30 を超える国/地域がサポートされ、その多くで複数の言語が使用されています。

必須ではありませんが、要求では [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) クエリ パラメーターを指定するべきです。このパラメーターは、結果取得元の市場を示します。 省略可能なクエリ パラメーターの一覧については、「[クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)」をご覧ください。

`cc` クエリ パラメーターを使用して国/地域を指定できます。 国/地域を指定する場合は、`Accept-Language` ヘッダーを使用して 1 つ以上の言語コードも指定する必要があります。 サポートされる言語は国/地域によって異なります。これらは、「**市場**」の表に国/地域ごとに示されています。

`Accept-Language` ヘッダーと `setLang` クエリ パラメーターは相互に排他的なので、両方指定することはできません。 詳細については、「[Accept-language](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage)」を参照してください。

## <a name="countriesregions"></a>国/リージョン

|国/リージョン|コード|
|-------|----|
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
|中国|CN|
|ポーランド|PL|
|ポルトガル|PT|
|フィリピン|PH|
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


## <a name="markets"></a>市場

|国/リージョン|言語|市場コード|
|-------|--------|-----------|
|アルゼンチン|Spanish|es-AR|
|オーストラリア|English|en-AU|
|オーストリア|German|de-AT|
|ベルギー|Dutch|nl-BE|
|ベルギー|French|fr-BE|
|ブラジル|Portuguese|pt-BR|
|Canada|English|en-CA|
|Canada|French|fr-CA|
|チリ|Spanish|es-CL|
|デンマーク|Danish|da-DK|
|フィンランド|Finnish|fi-FI|
|フランス|French|fr-FR|
|ドイツ|German|de-DE|
|香港特別行政区|Traditional Chinese|zh-HK|
|インド|English|en-IN|
|インドネシア|English|en-ID|
|イタリア|Italian|it-IT|
|日本|Japanese|ja-JP|
|韓国|Korean|ko-KR|
|マレーシア|English|en-MY|
|メキシコ|Spanish|es-MX|
|オランダ|Dutch|nl-NL|
|ニュージーランド|English|en-NZ|
|ノルウェー|ノルウェー語|no-NO|
|中国|中国語|zh-CN|
|ポーランド|Polish|pl-PL|
|ポルトガル|Portuguese|pt-PT|
|フィリピン|English|en-PH|
|ロシア|Russian|ru-RU|
|サウジアラビア|アラビア語|ar-SA|
|南アフリカ|English|en-ZA|
|スペイン|Spanish|es-ES|
|スウェーデン|Swedish|sv-SE|
|スイス|French|fr-CH|
|スイス|German|de-CH|
|台湾|Traditional Chinese|zh-TW|
|トルコ|Turkish|tr-TR|
|イギリス|English|en-GB|
|United States|English|ja-JP|
|United States|Spanish|es-US|
