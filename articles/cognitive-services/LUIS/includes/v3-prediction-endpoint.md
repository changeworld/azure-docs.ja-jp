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
ms.openlocfilehash: bc6e43faca47e360daa8214e6b9f6e9df4a2f130
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495352"
---
1. LUIS ポータルで、[Manage]\(管理\) セクション (右上のメニュー) の [Keys and endpoints]\(キーとエンドポイント\) ページ (左側のメニュー) で、ページの下部にあるエンドポイント URL を選択します。

    ブラウザー タブが開き、アドレス バーにエンドポイント URL が表示されます。

    URL には、アプリ ID、キー、およびスロット名が含まれます。 V3 予測エンドポイント URL は次のようになります。

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=UTTERANCE-TEXT.`

