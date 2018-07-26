---
title: Language Understanding (LUIS) リージョン | Microsoft Docs
titleSuffix: Azure
description: この記事は、LUIS Web サイト、Azure サブスクリプション、および世界の地域の LUIS リージョンの一覧を示しています。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: diberry
ms.openlocfilehash: 1f6090bf1ac588585a16f93d2ac091e8950ca45f
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238932"
---
# <a name="regions-and-keys"></a>リージョンとキー

LUIS アプリを公開するリージョンは、Azure LUIS エンドポイント キーを作成するときに Azure portal で指定するリージョンまたは場所に対応します。 [アプリを公開](./luis-how-to-publish-app.md)するとき、キーに関連付けられているリージョンに対して、エンドポイント URL が LUIS によって自動的に生成されます。 LUIS アプリを複数のリージョンに公開するには、リージョンごとに少なくとも 1 つのキーが必要です。 

## <a name="luis-website"></a>LUIS Web サイト
リージョンに基づいて 3 つの LUIS Web サイトがあります。 作成と公開は同じリージョンで行う必要があります。 

|LUIS|リージョン|
|--|--|
|[www.luis.ai][www.luis.ai]|米国<br>ヨーロッパ以外<br>オーストラリア以外|
|[au.luis.ai][au.luis.ai]|オーストラリア|
|[eu.luis.ai][eu.luis.ai]|ヨーロッパ|


## <a name="publishing-regions"></a>公開リージョン

https://www.luis.ai で作成された LUIS アプリは、[ヨーロッパ](#publishing-to-europe) リージョンと[オーストラリア](#publishing-to-australia) リージョンを除くすべてのエンドポイントに公開できます。 

オーサリング リージョン アプリは、対応する公開リージョンにのみ公開できます。 お使いのアプリが間違ったオーサリング リージョンにある場合は、アプリをエクスポートし、ご自身の公開リージョンの正しいオーサリング リージョンにインポートしてください。 

 グローバル リージョン | オーサリング リージョン | 公開およびクエリ実行リージョン   |   LUIS Web サイト | エンドポイント URL 形式   |
|-----|------|------|------|------|
| アジア | 米国西部| 東アジア     | [www.luis.ai][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| アジア | 米国西部| 東南アジア     | [www.luis.ai][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[オーストラリア](#publishing-to-australia) | オーストラリア東部| オーストラリア東部     |   [au.luis.ai][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[ヨーロッパ](#publishing-to-europe)| 西ヨーロッパ| 北ヨーロッパ     | [eu.luis.ai][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[ヨーロッパ](#publishing-to-europe) | 西ヨーロッパ| 西ヨーロッパ     | [eu.luis.ai][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 北米 | 米国西部 | 米国東部      |[www.luis.ai][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北米 | 米国西部 | 米国東部 2     | [www.luis.ai][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北米 | 米国西部 | 米国中南部     | [www.luis.ai][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 北米 | 米国西部 | 米国中西部     |[www.luis.ai][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北米 | 米国西部 | 米国西部 |  [www.luis.ai][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 北米 | 米国西部 | 米国西部 2    | [www.luis.ai][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 南アメリカ | 米国西部 | ブラジル南部     | [www.luis.ai][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>ヨーロッパへの公開

ヨーロッパ リージョンに公開するには、https://eu.luis.ai でのみ LUIS を作成します。 それ以外の場所で、ヨーロッパ リージョンのキーを使って公開しようとすると、LUIS によって警告メッセージが表示されます。 代わりに https://eu.luis.aiを使用してください。 [https://eu.luis.ai][eu.luis.ai] で作成された LUIS アプリは、他のリージョンに自動的には移行されません。 移行するには、その LUIS アプリをエクスポートしてから、インポートします。

## <a name="publishing-to-australia"></a>オーストラリアへの公開

オーストラリア リージョンに公開するには、https://au.luis.ai でのみ LUIS を作成します。 それ以外の場所で、オーストラリア リージョンのキーを使って公開しようとすると、LUIS によって警告メッセージが表示されます。 代わりに https://au.luis.aiを使用してください。 [https://au.luis.ai][au.luis.ai] で作成された LUIS アプリは、他のリージョンに自動的には移行されません。 移行するには、その LUIS アプリをエクスポートしてから、インポートします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [事前構築済みのエンティティのリファレンス](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai