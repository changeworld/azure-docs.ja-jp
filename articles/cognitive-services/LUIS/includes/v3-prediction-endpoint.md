---
title: V3 予測エンドポイントを取得する方法
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80287749"
---
1. LUIS ポータルの **[管理]** セクション (右上メニュー) の **[Azure リソース]** ページ (左メニュー) の **[Prediction Resources]** \(予測リソース\) タブのページ下部の **example Query**\(サンプル クエリ\) をコピーします。

    この URL を新しいブラウザー タブに貼り付けます。

    URL には、アプリ ID、キー、およびスロット名が含まれます。 V3 予測エンドポイント URL は次のようになります。

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

