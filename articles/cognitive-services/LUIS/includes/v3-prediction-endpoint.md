---
title: V3 予測エンドポイントを取得する方法
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: 85fcd0308083350a02afd4eadac2b361337f3b33
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128220"
---
1. LUIS ポータルの **[管理]** セクション (右上メニュー) の **[Azure リソース]** ページ (左メニュー) の **[Prediction Resources]\(予測リソース\)** タブのページ下部の **[example Query]\(サンプル クエリ\)** をコピーします。 URL には、アプリ ID、キー、およびスロット名が含まれます。 V3 予測エンドポイント URL の形式は次のとおりです: `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

    :::image type="content" source="../media/prediction-resources-example-query.png" alt-text="予測リソース セクションのクエリの例" lightbox="../media/prediction-resources-example-query.png":::
    
    この URL を新しいブラウザー タブに貼り付けます。URL が表示されない場合は、予測リソースがないため、作成する必要があります。 

    

    

