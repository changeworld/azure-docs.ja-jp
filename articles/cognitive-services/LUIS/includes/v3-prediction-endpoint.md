---
title: V3 予測エンドポイントを取得する方法
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: a607ad71915606d6046b4c71291a49b8641bdcc9
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545055"
---
1. LUIS ポータルの **[管理]** セクション (右上メニュー) の **[Azure リソース]** ページ (左メニュー) の **[Prediction Resources]\(予測リソース\)** タブのページ下部の **[example Query]\(サンプル クエリ\)** をコピーします。

    この URL を新しいブラウザー タブに貼り付けます。

    URL には、アプリ ID、キー、およびスロット名が含まれます。 V3 予測エンドポイント URL は次のようになります。

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

