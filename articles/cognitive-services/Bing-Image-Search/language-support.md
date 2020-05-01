---
title: 言語サポート - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Bing Image Search API でサポートされている国/地域と言語を確認します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: ca3821b6088e45730334d1b0971e270b1d86dfce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "68881919"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Bing Image Search API の言語と地域のサポート

Bing Image Search API では 30 を超える国/地域がサポートされ、その多くで複数の言語が使用されています。 クエリを使用して国/地域を指定すると、主に、その国/地域への関心に基づいて検索結果を絞り込むことができます。 さらに、これらの結果には Bing へのリンクを含めることができ、これらのリンクにより、指定された国/地域または言語に従って Bing ユーザー エクスペリエンスをローカライズできます。

国/地域と言語を指定するには、`mkt` (市場) クエリ パラメーターを下の「**市場**」の表のコードに設定します。 この市場は、国/地域と言語の両方を指定します。 表示テキストを別の言語で表示することを希望するユーザー向けには、`setLang` クエリ パラメーターを適切な言語コードに設定します。

あるいは、`cc` クエリ パラメーターを使用して国/地域を指定することもできます。 国/地域を指定する場合は、`Accept-Language` HTTP ヘッダーを使用して 1 つ以上の言語コードも指定する必要があります。 サポートされる言語は国/地域によって異なります。これらは、「市場」の表に国/地域ごとに示されています。

> [!NOTE]
> Trending Images API では、現在、次の市場のみがサポートされています。
> - en-US (英語、米国)
> - en-CA (英語、カナダ)
> - en-AU (英語、オーストラリア)
> - zh-CN (中国語、中国)

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
Bing News Search エンドポイントの詳細については、[News Image Search API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)を参照してください。
