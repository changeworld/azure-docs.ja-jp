---
title: V3 予測エンドポイントを取得する方法
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: 18f5422202cf972d49349cc04b845c623cabffa3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589146"
---
1. LUIS ポータルの **[管理]** セクション (右上メニュー) の **[Azure リソース]** ページ (左メニュー) の **[Prediction Resources]\(予測リソース\)** タブのページ下部の **[example Query]\(サンプル クエリ\)** をコピーします。

    この URL を新しいブラウザー タブに貼り付けます。

    URL には、アプリ ID、キー、およびスロット名が含まれます。 V3 予測エンドポイント URL は次のようになります。

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

