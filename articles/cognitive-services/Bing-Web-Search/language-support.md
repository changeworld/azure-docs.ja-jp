---
title: 言語サポート - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bing News Search API でサポートされる自然言語、国、および地域の一覧。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 9425de6e75a9a46d71ff85ce49b0650c8e7a9a16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "68882686"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Bing Web Search API の言語と地域のサポート

Bing Web Search API では 30 を超える国または地域がサポートされ、その多くで複数の言語が使用されています。 クエリでの国または地域の指定は、国または地域の関心に基づいた検索結果の絞り込みに役立ちます。 結果には Bing へのリンクを含めることができ、これらのリンクにより、指定された国/地域または言語に従って Bing ユーザー エクスペリエンスをローカライズできます。

`cc` クエリ パラメーターを使用して国または地域を指定できます。 国または地域を指定する場合、[`Accept-Language` ヘッダー](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers)を使用して 1 つ以上の言語コードも指定する必要があります。 各市場でサポートされている言語の一覧を確認するには、「[市場](#markets)」の表をご利用ください。

または、`mkt` クエリ パラメーターと「**市場**」の表のコードを使用して市場を指定できます。 市場を指定すると、同時に国または地域と優先言語が指定されます。 `setLang` クエリ パラメーターを使用すれば、言語を明示的に設定できます。

## <a name="countriesregions"></a>国/地域

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

## <a name="next-steps"></a>次のステップ

* [Bing Image Search API リファレンス](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
