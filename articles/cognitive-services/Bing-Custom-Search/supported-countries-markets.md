---
title: Azure の Bing Custom Search API でサポートされている国と言語 | Microsoft Docs
description: Bing Custom Search API でサポートされている国と言語を確認します。
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/19/2017
ms.author: v-gedod
ms.openlocfilehash: 7ff309f9b789662c4ebd791dffaa2bc2e440763e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374749"
---
# <a name="bing-custom-search-countries-and-languages"></a>Bing Custom Search でサポートされている国と言語

Bing Custom Search API では数十か国がサポートされ、その多くの国で複数の言語が使用されています。 

必須ではありませんが、要求では [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#mkt) クエリ パラメーターを指定するべきです。このパラメーターは、結果取得元の市場を示します。 省略可能なクエリ パラメーターの一覧については、「[クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)」をご覧ください。

`cc` クエリ パラメーターを使用して国を指定できます。 国を指定する場合は、`Accept-Language` ヘッダーを使用して 1 つ以上の言語コードを指定する必要もあります。 サポートされる言語は国によって異なります。**市場**の表に、各国でサポートされる言語を示します。

`Accept-Language` ヘッダーと `setLang` クエリ パラメーターは相互に排他的なので、両方指定することはできません。 詳細については、「[Accept-language](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#acceptlanguage)」を参照してください。

## <a name="countries"></a>国

|Country|コード|
|-------|----|
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
|米国|US|


## <a name="markets"></a>市場

|Country|言語|市場コード|
|-------|--------|-----------|
|アルゼンチン|スペイン語|es-AR|
|オーストラリア|英語|en-AU|
|オーストリア|ドイツ語|de-AT|
|ベルギー|オランダ語|nl-BE|
|ベルギー|フランス語|fr-BE|
|ブラジル|ポルトガル語|pt-BR|
|カナダ|英語|en-CA|
|カナダ|フランス語|fr-CA|
|チリ|スペイン語|es-CL|
|デンマーク|デンマーク語|da-DK|
|フィンランド|フィンランド語|fi-FI|
|フランス|フランス語|fr-FR|
|ドイツ|ドイツ語|de-DE|
|香港特別行政区|繁体字中国語|zh-HK|
|インド|英語|en-IN|
|インドネシア|英語|en-ID|
|イタリア|イタリア語|it-IT|
|日本|日本語|ja-JP|
|韓国|韓国語|ko-KR|
|マレーシア|英語|en-MY|
|メキシコ|スペイン語|es-MX|
|オランダ|オランダ語|nl-NL|
|ニュージーランド|英語|en-NZ|
|ノルウェー|ノルウェー語|no-NO|
|中国|中国|zh-CN|
|ポーランド|ポーランド語|pl-PL|
|ポルトガル|ポルトガル語|pt-PT|
|フィリピン|英語|en-PH|
|ロシア|ロシア語|ru-RU|
|サウジアラビア|アラビア語|ar-SA|
|南アフリカ|英語|en-ZA|
|スペイン|スペイン語|es-ES|
|スウェーデン|スウェーデン語|sv-SE|
|スイス|フランス語|fr-CH|
|スイス|ドイツ語|de-CH|
|台湾|繁体字中国語|zh-TW|
|トルコ|トルコ語|tr-TR|
|イギリス|英語|en-GB|
|米国|英語|en-US|
|米国|スペイン語|es-US|
