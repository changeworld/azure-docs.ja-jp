---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: a6e6c89c34723fb9e11b0c7e4ab8c9bedb8aa9ca
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959037"
---
**[Azure リソース]** ページ (左側メニュー) の **[管理]** セクション (右上メニュー) で、**サンプルクエリ**の URL をコピーし、新しいブラウザー タブに貼り付けます。

エンドポイント URL は次の形式になります。YOUR-CUSTOM-SUBDMAIN、APP-ID、KEY-ID は、自分のカスタム サブドメイン、アプリ ID、エンドポイント キーに置き換えます。

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```