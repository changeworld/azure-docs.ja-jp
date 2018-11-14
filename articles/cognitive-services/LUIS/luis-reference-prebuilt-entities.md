---
title: 作成済みエンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: この記事では、Language Understanding (LUIS) に含まれる作成済みエンティティの一覧を示します。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: bc23f2e5d8304400802c74093d4d78a1fcd8cf22
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219489"
---
# <a name="entities-per-culture"></a>カルチャごとのエンティティ

Language Understanding (LUIS) では、作成済みのエンティティが提供されています。 作成済みエンティティをアプリケーションに組み込むと、LUIS は対応するエンティティの予測をエンドポイントの応答に含めます。 すべての発話例にも、エンティティでラベルが付けられます。 作成済みエンティティの動作を変更することは**できません**。 特に記載のない限り、作成済みエンティティはすべての LUIS アプリケーション ロケール (カルチャ) で使用できます。 次の表では、各カルチャでサポートされている作成済みエンティティを示します。

作成済みエンティティ   |   英語 (米国)<br>```En-us```   |   フランス語 (フランス)<br>```fr-FR```   |   イタリア語 (イタリア)<br>```it-IT```   |   スペイン語 (スペイン)<br>```es-ES```   |   中国語<br>```zh-CN```   |   ドイツ語<br>```de-DE```   |   ポルトガル語 (ブラジル)<br>```pt-BR```   |   日本語 (日本)<br>```ja-JP```   |   韓国語 (韓国)<br>```ko-kr```   | フランス語 (カナダ)<br>```fr-CA```   |   スペイン語 (メキシコ)<br>```es-MX```   |   オランダ語 (オランダ)<br>```nl-NL```   |
------|:------:|------|------|------|------|------|------|------|------|------|------|------|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Currency](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   |   ✔   |   -   |   ✔   |    ✔   |   -   |   ✔   |   -   |   -   |   -   |   -   |   -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[電子メール](luis-reference-prebuilt-email.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   |   ✔   |   ✔   |   ✔   |   -   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |
[数値](luis-reference-prebuilt-number.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   |    -   |    -   |    -   |    ✔   |    -   |    -   |    -   |   -   |   -   |   -   |   -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[URL](luis-reference-prebuilt-url.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

「[非推奨のエンティティ](luis-reference-prebuilt-deprecated.md)」の注をご覧ください

KeyPhrase は、ポルトガル語 (ブラジル) - ```pt-BR``` のすべてのサブカルチャーで使用できるわけではありません。

## <a name="contribute-to-prebuilt-entity-cultures"></a>作成済みエンティティ カルチャへの寄与
作成済みエンティティは、Recognizers-Text オープンソース プロジェクトで開発されています。 プロジェクトに[寄与](https://github.com/Microsoft/Recognizers-Text)してください。 このプロジェクトには、カルチャごとの通貨の例が含まれています。 

GeographyV2 と PersonName は Recognizers-Text プロジェクトに含まれていません。 これらの事前構築済みエンティティの問題については、[サポート リクエスト](../../azure-supportability/how-to-create-azure-support-request.md)を開いてください。 

## <a name="next-steps"></a>次の手順

[number](luis-reference-prebuilt-number.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[currency](luis-reference-prebuilt-currency.md) エンティティについて学習します。 
