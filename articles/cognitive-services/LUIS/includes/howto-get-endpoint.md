---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 39bfa5b6800c65112850faa8842b915ceedb9312
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648067"
---
**[Azure リソース]** ページ (左側メニュー) の **[管理]** セクション (右上メニュー) で、**サンプルクエリ**の URL をコピーし、新しいブラウザー タブに貼り付け ます。 

エンドポイント URL は次の形式になります。APP-ID と KEY-ID は、自分のアプリ ID とエンドポイント キーに置き換えます。

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```