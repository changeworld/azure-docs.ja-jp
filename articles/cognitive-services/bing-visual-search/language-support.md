---
title: 言語サポート - Bing Visual Search API
titleSuffix: Azure Cognitive Services
description: Bing Visual Search API でサポートされる自然言語、国、および地域の一覧。 Bing Visual Search API は、30 を超え、その多くで複数の言語が使用されている国/地域をサポートしています。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: b17341bc234ff3dfecc2c6dcd84ef77116a95d61
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "68883548"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Bing Visual Search API の言語と地域のサポート

Bing Visual Search API では 30 を超える国/地域がサポートされ、その多くで複数の言語が使用されています。 各要求には、ユーザーに最適な国/地域と言語を含める必要があります。 ユーザーの市場を把握することにより、Bing が適切な結果を返しやすくなります。 国/地域と言語を指定しない場合、Bing は可能な限り、ユーザーの国/地域と言語を特定しようとします。 これらの結果には Bing へのリンクを含めることができるため、ユーザーが Bing のリンクをクリックすれば国/地域と言語がわかり、推奨されるローカライズされた Bing ユーザー エクスペリエンスが提供される可能性があります。

国/地域と言語を指定するには、`mkt` (市場) クエリ パラメーターを下の「**市場**」の表のコードに設定します。 この市場は、国/地域と言語の両方を指定します。 表示テキストを別の言語で表示することを希望するユーザー向けには、`setLang` クエリ パラメーターを適切な言語コードに設定します。

あるいは、`cc` クエリ パラメーターを使用して国/地域を指定することもできます。 国/地域を指定する場合は、`Accept-Language` HTTP ヘッダーを使用して 1 つ以上の言語コードも指定する必要があります。 サポートされる言語は国/地域によって異なります。これらは、「市場」の表に国ごとに示されています。



> [!NOTE]
> 市場には次の制限事項が適用されます。
>
> - 画像認識に注釈を付ける場合、使用できるのは英語のみです。
> - レシピ、ショッピング、画像を含むページなどの分析情報が使用できるのは en-US 市場のみです。


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
