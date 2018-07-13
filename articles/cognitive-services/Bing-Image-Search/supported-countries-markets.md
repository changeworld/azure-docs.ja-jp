---
title: Azure の Bing Image Search API でサポートされている国と言語 | Microsoft Docs
description: Bing Image Search API でサポートされている国と言語を確認します。
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: d0d33ee714ba5cd1ce4e846b96c04f755933bee1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376896"
---
# <a name="bing-image-search-countries-and-languages"></a>Bing Image Search でサポートされている国と言語

Bing Image Search API では数十か国がサポートされ、その多くの国で複数の言語が使用されています。 クエリを使用して国を指定すると、主にその国への関心に基づいて検索結果を絞り込むことができます。 さらに、結果には Bing へのリンクを含めることができます。これらのリンクでは、指定された国または言語に従って Bing ユーザー エクスペリエンスをローカライズすることができます。

国と言語を指定するには、`mkt` (市場) クエリ パラメーターを下記の「**市場**」の表のコードに設定します。 市場は、国と言語の両方を指定します。 表示テキストを別の言語で表示することを希望するユーザー向けには、`setLang` クエリ パラメーターを適切な言語コードに設定します。

または、`cc` クエリ パラメーターを使用して国を指定することもできます。 国を指定する場合は、`Accept-Language` HTTP ヘッダーを使用して 1 つ以上の言語コードを指定する必要もあります。 サポートされる言語は国によって異なります。「市場」の表に、各国でサポートされる言語を示します。

> [!NOTE]
> Trending Images API では、現在、次の市場のみがサポートされています。
> - en-US (英語、米国) 
> - en-CA (英語、カナダ) 
> - en-AU (英語、オーストラリア) 
> - zh-CN (中国語、中国)

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
|ノルウェー|いいえ|
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
|中国|中国語|zh-CN|
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

## <a name="next-steps"></a>次の手順
Bing News Search エンドポイントの詳細については、[News Image Search API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)を参照してください。
